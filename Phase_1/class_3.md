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

