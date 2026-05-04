# Class 4: Service Users, Directory Ownership & Secure Permissions

---

## 1. What is a Service User?

A **service user** is a system account created specifically to run one application or service.  
It is not a human user — no one logs in with it.  
Its only purpose is to own and run a specific process with limited privileges.

### Why Use a Service User?

| Without Service User | With Service User |
|---------------------|------------------|
| Service runs as root | Service runs with least privilege |
| One breach = full system access | One breach = only that service's files |
| No audit trail per service | Clear ownership of files and processes |
| Dangerous in production | Standard secure practice |

### Characteristics of a Service User

- No home directory (or a restricted one like `/opt/appname`)
- No interactive login shell (`/usr/sbin/nologin` or `/bin/false`)
- UID in system range (1–999) using `-r` flag
- Owns only the files and directories its service needs

---

## 2. Create a Service User

### Basic Syntax

```bash
sudo useradd [options] username
```

### Key Flags for Service Users

| Flag | Meaning |
|------|---------|
| `-r` | Create as system user (UID < 1000, no expiry) |
| `-s /usr/sbin/nologin` | No interactive login shell |
| `-M` | Do NOT create a home directory |
| `-d /path` | Set a custom home/working directory |
| `-c "description"` | Add a comment/description |
| `-g groupname` | Set primary group |

### Create a Service User — Step by Step

**Example: create a user for a custom app called `myapp`**

```bash
# Step 1: Create the service group first
sudo groupadd myapp

# Step 2: Create the service user
sudo useradd \
  -r \
  -s /usr/sbin/nologin \
  -M \
  -d /opt/myapp \
  -g myapp \
  -c "myapp service account" \
  myapp
```

**Verify the user was created correctly:**

```bash
id myapp
```

Output:

```
uid=998(myapp) gid=998(myapp) groups=998(myapp)
```

```bash
grep myapp /etc/passwd
```

Output:

```
myapp:x:998:998:myapp service account:/opt/myapp:/usr/sbin/nologin
```

**Confirm the user cannot log in:**

```bash
sudo -u myapp bash
# Output: This account is currently not available.
```

---

## 3. Create and Organize the Service Directory Structure

After creating the service user, set up the directories the service needs.  
Each directory has a different purpose and needs different permissions.

### Common Directory Layout for a Service

```
/opt/myapp/          → Application files (binaries, code)
/etc/myapp/          → Configuration files
/var/log/myapp/      → Log files (service writes here)
/var/run/myapp/      → PID files and sockets
/var/lib/myapp/      → Data files (databases, state)
```

### Create All Directories

```bash
sudo mkdir -p /opt/myapp
sudo mkdir -p /etc/myapp
sudo mkdir -p /var/log/myapp
sudo mkdir -p /var/run/myapp
sudo mkdir -p /var/lib/myapp
```

**Why use these standard paths?**

| Path | Standard Use | Reason |
|------|-------------|--------|
| `/opt/appname` | App binaries | Isolated from system binaries |
| `/etc/appname` | Config files | Standard config location on Linux |
| `/var/log/appname` | Logs | Survives reboots, easy to rotate |
| `/var/run/appname` | PID/sockets | Temporary — cleared on reboot |
| `/var/lib/appname` | Persistent data | Survives reboots |

---

## 4. Assign Directory Ownership

After creating the directories, assign ownership to the correct user and group.  
The service user must own the directories it needs to write to.

### Assign Ownership with chown

```bash
# App binaries — owned by root (service should not modify its own binaries)
sudo chown root:root /opt/myapp

# Config — owned by root, readable by service group
sudo chown root:myapp /etc/myapp

# Logs — service must write here
sudo chown myapp:myapp /var/log/myapp

# PID/socket files — service creates these at runtime
sudo chown myapp:myapp /var/run/myapp

# Data files — service reads and writes
sudo chown myapp:myapp /var/lib/myapp
```

### Verify Ownership

```bash
ls -ld /opt/myapp /etc/myapp /var/log/myapp /var/run/myapp /var/lib/myapp
```

Expected output:

```
drwxr-xr-x 2 root  root  4096 Apr 28 myapp /opt/myapp
drwxr-x--- 2 root  myapp 4096 Apr 28 myapp /etc/myapp
drwxr-x--- 2 myapp myapp 4096 Apr 28 myapp /var/log/myapp
drwxr-x--- 2 myapp myapp 4096 Apr 28 myapp /var/run/myapp
drwxr-x--- 2 myapp myapp 4096 Apr 28 myapp /var/lib/myapp
```

**Rule of thumb:**

> Directories the service only reads → owned by `root`, group `myapp`  
> Directories the service writes to → owned by `myapp:myapp`

---

## 5. Set Secure Permissions on Each Directory

Different directories need different permission levels based on what the service does with them.

### Permission Plan

```bash
# App binaries — root owned, everyone can read/execute, no one can write
sudo chmod 755 /opt/myapp

# Config — root owned, only owner+group can access (no others)
sudo chmod 750 /etc/myapp

# Logs — service user owns, group can read (for log shipping tools), no others
sudo chmod 750 /var/log/myapp

# PID/socket — service user owns, private
sudo chmod 750 /var/run/myapp

# Data — service user owns, private
sudo chmod 750 /var/lib/myapp
```

### Permission Breakdown

| Directory | chmod | Owner Perms | Group Perms | Others |
|-----------|-------|------------|------------|--------|
| `/opt/myapp` | 755 | rwx | r-x | r-x |
| `/etc/myapp` | 750 | rwx | r-x | --- |
| `/var/log/myapp` | 750 | rwx | r-x | --- |
| `/var/run/myapp` | 750 | rwx | r-x | --- |
| `/var/lib/myapp` | 750 | rwx | r-x | --- |

### Why 750 and Not 777?

```
750 = rwxr-x---
```

- `rwx` → owner (service user) can read, write, execute
- `r-x` → group can read and enter the directory
- `---` → others (everyone else) get nothing

Using `777` would let any user on the system read your logs and config — a security risk in production.

### Set Config File Permissions

Config files inside `/etc/myapp` need tighter control:

```bash
sudo chmod 640 /etc/myapp/app.conf
```

```
640 = rw-r-----
```

- Owner (root): can read and write
- Group (myapp): can only read — service reads its own config
- Others: no access

---

## 6. Complete Setup Script — All Steps Together

This is a full real-world example of setting up a service user from scratch:

```bash
#!/bin/bash
# Full secure service setup for "myapp"

APP_NAME="myapp"

# 1. Create group and service user
sudo groupadd $APP_NAME
sudo useradd \
  -r \
  -s /usr/sbin/nologin \
  -M \
  -d /opt/$APP_NAME \
  -g $APP_NAME \
  -c "$APP_NAME service account" \
  $APP_NAME

# 2. Create all required directories
sudo mkdir -p /opt/$APP_NAME
sudo mkdir -p /etc/$APP_NAME
sudo mkdir -p /var/log/$APP_NAME
sudo mkdir -p /var/run/$APP_NAME
sudo mkdir -p /var/lib/$APP_NAME

# 3. Assign ownership
sudo chown root:root     /opt/$APP_NAME
sudo chown root:$APP_NAME /etc/$APP_NAME
sudo chown $APP_NAME:$APP_NAME /var/log/$APP_NAME
sudo chown $APP_NAME:$APP_NAME /var/run/$APP_NAME
sudo chown $APP_NAME:$APP_NAME /var/lib/$APP_NAME

# 4. Set permissions
sudo chmod 755 /opt/$APP_NAME
sudo chmod 750 /etc/$APP_NAME
sudo chmod 750 /var/log/$APP_NAME
sudo chmod 750 /var/run/$APP_NAME
sudo chmod 750 /var/lib/$APP_NAME

# 5. Verify everything
echo "--- User Info ---"
id $APP_NAME
echo "--- Directory Ownership & Permissions ---"
ls -ld /opt/$APP_NAME /etc/$APP_NAME /var/log/$APP_NAME /var/run/$APP_NAME /var/lib/$APP_NAME
```

Run the script:

```bash
chmod +x setup-service.sh
sudo bash setup-service.sh
```

---

## 7. Incident: Permission Denied for Service Writing Logs

### Problem

The `myapp` service starts but immediately crashes.  
Checking the system journal shows:

```bash
sudo journalctl -u myapp --no-pager
```

Output:

```
May 05 10:00:01 server myapp[1234]: FATAL: cannot open log file for writing
May 05 10:00:01 server myapp[1234]: open /var/log/myapp/app.log: permission denied
May 05 10:00:01 server systemd[1]: myapp.service: Main process exited, code=exited
```

The service cannot write its logs. Let us investigate and fix it securely.

### Investigation

**Step 1: Check what user the service runs as**

```bash
sudo systemctl show myapp -p User
```

Output:

```
User=myapp
```

The service runs as `myapp`. Now check if that user can write to the log directory.

**Step 2: Check ownership and permissions of the log directory**

```bash
ls -ld /var/log/myapp
```

Output:

```
drwxr-xr-x 2 root root 4096 May 05 10:00 /var/log/myapp
```

Found the problem — the directory is owned by `root:root`.  
The `myapp` service user has no write permission here (`r-x` for others at best, but in this case `root` group).

**Step 3: Check if the log file exists and who owns it**

```bash
ls -la /var/log/myapp/
```

Output:

```
total 8
drwxr-xr-x 2 root root 4096 May 05 10:00 .
drwxr-xr-x 8 root root 4096 May 05 10:00 ..
```

No log file yet — the service cannot even create it because it does not own the directory.

**Step 4: Confirm which user the process tries to run as**

```bash
ps aux | grep myapp
```

Or switch to the service user and test:

```bash
sudo -u myapp touch /var/log/myapp/test.log
```

Output:

```
touch: cannot touch '/var/log/myapp/test.log': Permission denied
```

This confirms the service user `myapp` cannot write to that directory.

### Root Cause

The log directory `/var/log/myapp` was created but ownership was never transferred to the service user.  
It stayed owned by `root` — so `myapp` has no write access.

### The Wrong Fix — Do NOT Do This

Many people solve permission denied errors by doing:

```bash
sudo chmod 777 /var/log/myapp    # WRONG — dangerous!
```

This gives every user on the system full read and write access to the logs.  
Log files can contain sensitive data: API keys, passwords, internal IPs.  
`chmod 777` is never acceptable in production.

### The Correct Secure Fix

**Fix the ownership — give the log directory to the service user:**

```bash
sudo chown myapp:myapp /var/log/myapp
```

**Set secure permissions — only the service user and its group:**

```bash
sudo chmod 750 /var/log/myapp
```

**Verify the fix:**

```bash
ls -ld /var/log/myapp
```

Output:

```
drwxr-x--- 2 myapp myapp 4096 May 05 10:05 /var/log/myapp
```

**Test as the service user:**

```bash
sudo -u myapp touch /var/log/myapp/test.log
ls -la /var/log/myapp/
```

Output:

```
-rw-r--r-- 1 myapp myapp 0 May 05 10:05 test.log
```

Write works. Now restart the service:

```bash
sudo systemctl restart myapp
sudo systemctl status myapp
```

Service is now running and writing logs successfully.

### Incident Summary

| Step | What we did |
|------|------------|
| 1 | Found the error in journal logs |
| 2 | Checked who owns `/var/log/myapp` |
| 3 | Found it was owned by `root` not `myapp` |
| 4 | Confirmed with `sudo -u myapp touch` test |
| 5 | Fixed with `chown myapp:myapp` |
| 6 | Set `chmod 750` — NOT 777 |
| 7 | Verified, restarted service |

### Common Permission Mistakes and Fixes

| Mistake | Symptom | Wrong Fix | Correct Fix |
|---------|---------|-----------|------------|
| Directory owned by root | `permission denied` on write | `chmod 777` | `chown service:service /dir` |
| Log file owned by root | Service can't append logs | `chmod 666` on file | `chown service:service /var/log/app/` |
| Config not readable | App crashes on startup | `chmod 644` exposing secrets | `chown root:service` + `chmod 640` |
| Wrong user in service file | Process runs as wrong user | Run as root | Set `User=` in systemd unit file |

---

## 8. Lab: Hands-On Practice

### Task 1: Create a Service User for a Web App

```bash
# Create group
sudo groupadd webapp

# Create service user
sudo useradd -r -s /usr/sbin/nologin -M -g webapp -c "webapp service" webapp

# Verify
id webapp
grep webapp /etc/passwd
```

### Task 2: Set Up Directory Structure

```bash
# Create directories
sudo mkdir -p /opt/webapp
sudo mkdir -p /etc/webapp
sudo mkdir -p /var/log/webapp
sudo mkdir -p /var/lib/webapp

# Assign ownership
sudo chown root:root /opt/webapp
sudo chown root:webapp /etc/webapp
sudo chown webapp:webapp /var/log/webapp
sudo chown webapp:webapp /var/lib/webapp

# Set permissions
sudo chmod 755 /opt/webapp
sudo chmod 750 /etc/webapp
sudo chmod 750 /var/log/webapp
sudo chmod 750 /var/lib/webapp

# Verify all at once
ls -ld /opt/webapp /etc/webapp /var/log/webapp /var/lib/webapp
```

### Task 3: Reproduce the Incident and Fix It

```bash
# Step 1: Break it on purpose — set wrong ownership
sudo chown root:root /var/log/webapp

# Step 2: Try writing as service user — it will fail
sudo -u webapp touch /var/log/webapp/test.log

# Step 3: Fix it correctly
sudo chown webapp:webapp /var/log/webapp
sudo chmod 750 /var/log/webapp

# Step 4: Test again — it works now
sudo -u webapp touch /var/log/webapp/test.log
ls -la /var/log/webapp/
```

### Task 4: Harden a Config File

```bash
# Create a config file with a "secret"
sudo bash -c 'echo "DB_PASSWORD=supersecret123" > /etc/webapp/config.conf'

# Set ownership — root writes, webapp group reads only
sudo chown root:webapp /etc/webapp/config.conf
sudo chmod 640 /etc/webapp/config.conf

# Verify
ls -l /etc/webapp/config.conf

# Test — webapp user can read it
sudo -u webapp cat /etc/webapp/config.conf

# Test — another random user cannot
sudo -u nobody cat /etc/webapp/config.conf
# Output: Permission denied
```

