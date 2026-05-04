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

