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

