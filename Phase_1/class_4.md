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

