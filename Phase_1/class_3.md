# Class 3: Users, Groups, Permissions & File Ownership

---

## 1. Users in Linux

Linux is a multi-user operating system. Every process and file is owned by a user.  
Each user has a unique **UID (User ID)** number the system uses internally.

### Types of Users

| Type | UID Range | Purpose |
|------|-----------|---------|
| **root** | 0 | Superuser — full control over the system |
| **System users** | 1 – 999 | Created by services (nginx, mysql, www-data) |
| **Regular users** | 1000+ | Human users created manually |

### View All Users

```bash
cat /etc/passwd
```

Each line format:

```
username:x:UID:GID:comment:home_dir:shell
```

Example:

```
taimoor:x:1000:1000:Taimoor Kamran:/home/taimoor:/bin/bash
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
```

- `x` means password is stored in `/etc/shadow`
- `/usr/sbin/nologin` means the user cannot log in interactively (service account)

### User Management Commands

**Create a new user:**

```bash
sudo useradd username              # Basic user creation
sudo useradd -m username           # Create with home directory
sudo useradd -m -s /bin/bash username   # Set shell to bash
sudo useradd -m -u 1500 username   # Assign a specific UID
```

**Set or change password:**

```bash
sudo passwd username
```

**Modify an existing user:**

```bash
sudo usermod -s /bin/bash username     # Change shell
sudo usermod -d /new/home username     # Change home directory
sudo usermod -l newname oldname        # Rename user
sudo usermod -aG sudo username         # Add user to sudo group
```

**Delete a user:**

```bash
sudo userdel username          # Delete user only
sudo userdel -r username       # Delete user + home directory
```

**Check who you are:**

```bash
whoami          # Print current username
id              # Show UID, GID and group memberships
id username     # Check another user's info
```

---

## 2. Groups in Linux

A **group** is a collection of users that share the same access permissions.  
Every user belongs to a **primary group** (same name as user by default) and can belong to multiple **secondary groups**.  
Each group has a unique **GID (Group ID)**.

### Types of Groups

| Type | Purpose |
|------|---------|
| **Primary group** | Default group for new files created by the user |
| **Secondary groups** | Extra groups for shared access (e.g., `sudo`, `docker`) |

### View All Groups

```bash
cat /etc/group
```

Each line format:

```
groupname:x:GID:member1,member2
```

Example:

```
sudo:x:27:taimoor
docker:x:999:taimoor,deploy
www-data:x:33:
```

**Check groups for a user:**

```bash
groups                  # Your groups
groups username         # Another user's groups
```

### Group Management Commands

**Create a group:**

```bash
sudo groupadd devteam
sudo groupadd -g 1500 devteam     # Create with specific GID
```

**Add a user to a group:**

```bash
sudo usermod -aG devteam taimoor     # -a = append, -G = secondary group
sudo usermod -aG docker taimoor      # Add to docker group
```

> **Important:** After adding a user to a group, the user must log out and log back in for changes to take effect. Or run:

```bash
newgrp devteam
```

**Remove a user from a group:**

```bash
sudo gpasswd -d taimoor devteam
```

**Delete a group:**

```bash
sudo groupdel devteam
```

**Rename a group:**

```bash
sudo groupmod -n newname oldname
```

---

## 3. chmod — Change File Permissions

`chmod` controls who can read, write, or execute a file.

### Two Ways to Use chmod

#### Numeric (Octal) Method

Each permission has a value:

| Permission | Value |
|-----------|-------|
| `r` read  | 4     |
| `w` write | 2     |
| `x` execute | 1   |
| `-` none  | 0     |

Add them together for each group (owner, group, others):

```
chmod 754 file.txt
```

Breaking down `754`:

| Who    | Value | Permission |
|--------|-------|-----------|
| Owner  | 7 (4+2+1) | rwx |
| Group  | 5 (4+0+1) | r-x |
| Others | 4 (4+0+0) | r-- |

Common examples:

```bash
chmod 777 file.txt    # rwxrwxrwx — full access for everyone (avoid!)
chmod 755 script.sh   # rwxr-xr-x — standard for scripts
chmod 644 file.txt    # rw-r--r-- — standard for text files
chmod 600 secret.txt  # rw------- — private, owner only
chmod 400 key.pem     # r-------- — read-only, owner only (SSH keys)
chmod 000 file.txt    # ---------- — no access for anyone
```

#### Symbolic Method

More readable — uses letters instead of numbers.

Symbols:

| Who | Symbol |
|-----|--------|
| Owner (user) | `u` |
| Group | `g` |
| Others | `o` |
| All three | `a` |

Operators:

| Operator | Meaning |
|----------|---------|
| `+` | Add permission |
| `-` | Remove permission |
| `=` | Set exact permission |

Examples:

```bash
chmod +x script.sh          # Add execute for everyone
chmod u+x script.sh         # Add execute for owner only
chmod g-w file.txt          # Remove write from group
chmod o-r secret.txt        # Remove read from others
chmod a+r file.txt          # Add read for all (owner+group+others)
chmod u=rwx,g=rx,o= file    # Set exact: owner=rwx, group=rx, others=none
chmod -R 755 /var/www/html  # Apply recursively to all files in directory
```

---

## 4. chown — Change File Ownership

`chown` changes who owns a file or directory.  
Only root can change file ownership.

### Syntax

```bash
chown owner file
chown owner:group file
chown :group file          # Change group only
```

### Examples

```bash
sudo chown taimoor file.txt              # Change owner to taimoor
sudo chown taimoor:devteam file.txt      # Change owner and group
sudo chown :devteam file.txt             # Change group only
sudo chown -R nginx:nginx /var/www/html  # Change recursively (entire directory)
sudo chown -R www-data:www-data /var/www # Set web server ownership
```

### Check Current Ownership

```bash
ls -l file.txt
```

Output:

```
-rw-r--r-- 1 taimoor devteam 1024 Apr 28 10:00 file.txt
              ^^^^^^^  ^^^^^^^
              owner    group
```

### chown vs chmod — What is the Difference?

| Command | Changes | Who Can Run |
|---------|---------|-------------|
| `chmod` | Permissions (read/write/execute) | Owner or root |
| `chown` | Ownership (who the owner is) | Root only |

---

## 5. umask — Default Permission Mask

`umask` defines the **default permissions removed** when a new file or directory is created.  
It acts as a filter — it subtracts permissions from the maximum default.

### Default Maximum Permissions

| Type | Max permissions |
|------|----------------|
| File | 666 (rw-rw-rw-) — no execute by default |
| Directory | 777 (rwxrwxrwx) |

### How umask Works

```
Final permission = Maximum permission - umask
```

Example with `umask 022` (most common default):

| | Files | Directories |
|--|-------|------------|
| Max | 666 | 777 |
| umask | 022 | 022 |
| Result | **644** (rw-r--r--) | **755** (rwxr-xr-x) |

### Check Current umask

```bash
umask         # Shows current umask (e.g. 0022)
umask -S      # Shows in symbolic format (e.g. u=rwx,g=rx,o=rx)
```

### Set umask Temporarily

```bash
umask 027     # New files: 640, New dirs: 750
umask 077     # New files: 600, New dirs: 700 (private)
umask 022     # Default — restore standard umask
```

### Set umask Permanently

Add to `~/.bashrc` or `~/.profile`:

```bash
echo "umask 027" >> ~/.bashrc
source ~/.bashrc
```

### Common umask Values

| umask | File result | Dir result | Use case |
|-------|------------|-----------|---------|
| `022` | 644 | 755 | Standard (most systems) |
| `027` | 640 | 750 | Secure — group can read, others blocked |
| `077` | 600 | 700 | Private — only owner can access |
| `002` | 664 | 775 | Team collaboration |

---

## 6. File Ownership Models for Services

When services like Nginx, MySQL, or Docker run on Linux, they do **not** run as root.  
Each service gets its own **system user** for security isolation.  
If a service is hacked, the attacker can only access what that service user owns.

### Why Services Have Dedicated Users

- Limits damage if the service is compromised
- Files owned by the service user cannot be modified by other users
- Follows the **principle of least privilege**

### Common Service Users

| Service | System User | Group | Files It Owns |
|---------|------------|-------|--------------|
| Nginx   | `www-data` | `www-data` | `/var/www/html`, `/etc/nginx` |
| Apache  | `www-data` | `www-data` | `/var/www/html` |
| MySQL   | `mysql`    | `mysql`    | `/var/lib/mysql` |
| PostgreSQL | `postgres` | `postgres` | `/var/lib/postgresql` |
| Docker  | `root` (daemon) | `docker` | `/var/lib/docker` |
| SSH     | `sshd`     | `nogroup`  | `/var/run/sshd` |

### Real-World Example: Nginx Web Server

When Nginx serves a website, it reads files from `/var/www/html`.  
Those files must be owned by `www-data` or be readable by it.

**Set correct ownership for Nginx:**

```bash
sudo chown -R www-data:www-data /var/www/html
sudo chmod -R 755 /var/www/html
```

**Verify:**

```bash
ls -la /var/www/html
```

Output:

```
drwxr-xr-x 2 www-data www-data 4096 Apr 28 10:00 .
-rw-r--r-- 1 www-data www-data 1234 Apr 28 10:00 index.html
```

**What happens if ownership is wrong:**

```bash
# If files are owned by root and Nginx (www-data) cannot read them:
# Browser gets: 403 Forbidden
```

### Real-World Example: MySQL Database

MySQL stores data in `/var/lib/mysql` owned by the `mysql` user.

```bash
ls -la /var/lib/mysql
```

Output:

```
drwx------ 6 mysql mysql 4096 Apr 28 10:00 mysql
```

Permission `700` means only the `mysql` user can access the data directory.  
This prevents any other user or service from reading raw database files.

### Real-World Example: Application Deployment

When deploying an app, create a dedicated system user for it:

```bash
# Create a system user (no home, no login shell)
sudo useradd -r -s /usr/sbin/nologin myapp

# Give it ownership of the app files
sudo chown -R myapp:myapp /opt/myapp

# Set permissions
sudo chmod -R 750 /opt/myapp
sudo chmod -R 640 /opt/myapp/config/
```

This way the app runs with minimal privileges and config files stay private.

---

## 7. Lab: Hands-On Practice

### Task 1: Create Users and Groups

```bash
# Create a group for developers
sudo groupadd devteam

# Create two users and add to the group
sudo useradd -m -s /bin/bash alice
sudo useradd -m -s /bin/bash bob
sudo passwd alice
sudo passwd bob

# Add both to devteam group
sudo usermod -aG devteam alice
sudo usermod -aG devteam bob

# Verify
groups alice
groups bob
cat /etc/group | grep devteam
```

### Task 2: Practice chmod

```bash
# Create test files
mkdir ~/permission-lab
touch ~/permission-lab/script.sh
touch ~/permission-lab/data.txt
touch ~/permission-lab/secret.key

# Set permissions
chmod 755 ~/permission-lab/script.sh    # Executable script
chmod 644 ~/permission-lab/data.txt     # Normal file
chmod 600 ~/permission-lab/secret.key   # Private file

# Verify all at once
ls -l ~/permission-lab/
```

### Task 3: Practice chown

```bash
# Create a shared directory
sudo mkdir /opt/shared-project

# Create a group for it
sudo groupadd project-team
sudo usermod -aG project-team alice
sudo usermod -aG project-team bob

# Set ownership to alice, group to project-team
sudo chown alice:project-team /opt/shared-project
sudo chmod 770 /opt/shared-project   # Owner+group full access, others none

# Verify
ls -ld /opt/shared-project
```

### Task 4: Test umask

```bash
# Check current umask
umask

# Create files with default umask
touch ~/default-file.txt
mkdir ~/default-dir

# Check permissions
ls -la ~/ | grep default

# Change umask and create new files
umask 077
touch ~/private-file.txt
mkdir ~/private-dir

# Compare permissions
ls -la ~/ | grep -E "default|private"
```

