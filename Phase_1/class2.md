# Class 2: Linux Filesystem, Paths, Permissions & Core Commands

---

## 1. Linux Filesystem Overview

Linux organizes everything in a single tree starting from the root `/`.  
Every file, device, and directory lives under this root.  
Unlike Windows (C:\, D:\), Linux has one unified directory tree.

---

## 2. Important Directories

### /etc — Configuration Files

`/etc` stores system-wide configuration files.  
All application and service settings live here.  
Only the root user can modify files in `/etc`.

Examples:

```bash
/etc/passwd       # User account information
/etc/hosts        # Hostname to IP mappings
/etc/ssh/sshd_config  # SSH server configuration
/etc/nginx/nginx.conf # Nginx web server config
```

---

### /var — Variable Data

`/var` stores data that changes frequently during system operation.  
Logs, caches, mail spools, and runtime data are kept here.  
This is where you look when an application is misbehaving.

Examples:

```bash
/var/log/syslog        # General system logs
/var/log/auth.log      # Authentication logs
/var/log/nginx/        # Nginx access and error logs
/var/cache/            # Cached application data
```

---

### /home — User Home Directories

`/home` contains personal directories for each user on the system.  
Each user gets their own folder: `/home/username`.  
Users store personal files, configs, and projects here.

Examples:

```bash
/home/taimoor/          # Taimoor's home directory
/home/taimoor/.bashrc   # User shell configuration
/home/taimoor/projects/ # Personal project files
```

Shortcut: `~` always refers to the current user's home directory.

```bash
cd ~   # Go to your home directory
```

---

## 3. Paths in Linux

### Absolute Path

An absolute path starts from the root `/` and gives the full location.  
It works from anywhere in the filesystem.

```bash
/home/taimoor/projects/app.py
/etc/nginx/nginx.conf
/var/log/syslog
```

### Relative Path

A relative path is based on your current location.  
It does not start with `/`.

```bash
projects/app.py       # If you are already in /home/taimoor
../etc/nginx.conf     # Go one level up then into etc
./script.sh           # Current directory
```

Key symbols:

| Symbol | Meaning |
|--------|---------|
| `/`    | Root of filesystem |
| `~`    | Current user's home |
| `.`    | Current directory |
| `..`   | Parent directory |

---

## 4. File Permissions Basics

Every file and directory in Linux has permissions assigned to three groups:

| Group | Meaning |
|-------|---------|
| **Owner (u)** | The user who created the file |
| **Group (g)** | Users in the same group |
| **Others (o)** | Everyone else |

Each group has three permission types:

| Symbol | Permission | Value |
|--------|-----------|-------|
| `r`    | Read      | 4     |
| `w`    | Write     | 2     |
| `x`    | Execute   | 1     |
| `-`    | No permission | 0 |

### Reading Permissions

```bash
ls -l
```

Output example:

```
-rwxr-xr--  1 taimoor devs  1024 Apr 28 10:00 script.sh
```

Breaking it down:

```
- rwx r-x r--
│  │   │   │
│  │   │   └── Others: read only
│  │   └────── Group: read + execute
│  └────────── Owner: read + write + execute
└───────────── File type (- = file, d = directory)
```

### Changing Permissions with chmod

```bash
chmod 755 script.sh   # rwxr-xr-x  (numeric method)
chmod +x script.sh    # Add execute permission (symbolic method)
chmod u-w file.txt    # Remove write from owner
```

Common permission numbers:

| Number | Permission | Use case |
|--------|-----------|---------|
| 777    | rwxrwxrwx | Full access (dangerous) |
| 755    | rwxr-xr-x | Web files, scripts |
| 644    | rw-r--r-- | Regular files |
| 400    | r-------- | Private keys (.pem) |

---

## 5. Core Commands

### ls — List Directory Contents

`ls` shows files and directories in the current location.

```bash
ls              # Basic list
ls -l           # Long format with permissions, size, date
ls -a           # Show hidden files (starting with .)
ls -la          # Long format + hidden files
ls -lh          # Human-readable file sizes
ls /etc         # List a specific directory
```

