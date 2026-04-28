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

---

### cd — Change Directory

`cd` is used to navigate between directories.

```bash
cd /etc             # Go to /etc (absolute path)
cd projects         # Go into projects folder (relative path)
cd ..               # Go one level up (parent directory)
cd ~                # Go to home directory
cd -                # Go back to previous directory
```

---

### pwd — Print Working Directory

`pwd` shows your current location in the filesystem.

```bash
pwd
```

Output:

```
/home/taimoor/projects
```

Always use `pwd` when you are lost in the terminal.

---

### cp — Copy Files and Directories

`cp` copies files or directories from one place to another.

```bash
cp file.txt /tmp/            # Copy file to /tmp
cp file.txt backup.txt       # Copy and rename
cp -r myfolder /tmp/         # Copy entire directory (-r = recursive)
cp -i file.txt /tmp/         # Prompt before overwrite
```

---

### mv — Move or Rename Files

`mv` moves files/directories OR renames them.

```bash
mv file.txt /tmp/            # Move file to /tmp
mv oldname.txt newname.txt   # Rename file
mv myfolder /home/taimoor/   # Move entire folder
```

---

### rm — Remove Files and Directories

`rm` permanently deletes files or directories. There is no trash/recycle bin.

```bash
rm file.txt           # Delete a file
rm -r myfolder        # Delete directory and all contents
rm -f file.txt        # Force delete without prompt
rm -rf myfolder       # Force delete directory (use with caution!)
```

> **Warning:** `rm -rf` is irreversible. Always double-check the path before running.

---

### cat — Concatenate and Display File Contents

`cat` prints file contents to the terminal. Good for small files.

```bash
cat file.txt                  # Display file contents
cat /etc/passwd               # View system users file
cat file1.txt file2.txt       # Display multiple files
cat file1.txt >> file2.txt    # Append file1 contents to file2
```

---

### less — View Large Files Page by Page

`less` opens a file for scrolling — better than `cat` for large files.

```bash
less /var/log/syslog    # Open large log file
```

Navigation inside `less`:

| Key        | Action |
|------------|--------|
| `Space`    | Next page |
| `b`        | Previous page |
| `/keyword` | Search forward |
| `n`        | Next search result |
| `q`        | Quit |

---

### grep — Search Inside Files

`grep` searches for a pattern inside files and prints matching lines.

```bash
grep "error" /var/log/syslog          # Find lines with "error"
grep -i "error" /var/log/syslog       # Case-insensitive search
grep -r "password" /etc/              # Search recursively in directory
grep -n "failed" /var/log/auth.log    # Show line numbers
grep -v "info" /var/log/syslog        # Show lines NOT matching
grep "ERROR" *.log                    # Search in all .log files
```

Combine with `cat` or `less` using pipe `|`:

```bash
cat /var/log/syslog | grep "error"
```

---

## 6. Lab: Hands-On Practice

### Task 1: Create a Directory Structure

```bash
mkdir -p ~/devops-lab/app/config
mkdir -p ~/devops-lab/app/logs
mkdir -p ~/devops-lab/backup
```

Verify:

```bash
ls -R ~/devops-lab
```

### Task 2: Create and Copy Files

```bash
# Create a sample config file
echo "APP_PORT=8080" > ~/devops-lab/app/config/app.conf
echo "DB_HOST=localhost" >> ~/devops-lab/app/config/app.conf

# View it
cat ~/devops-lab/app/config/app.conf

# Copy config to backup
cp ~/devops-lab/app/config/app.conf ~/devops-lab/backup/
```

### Task 3: Move Files

```bash
# Create a temp log file
echo "2024-04-28 INFO App started" > ~/devops-lab/app.log

# Move it to logs directory
mv ~/devops-lab/app.log ~/devops-lab/app/logs/

# Confirm
ls ~/devops-lab/app/logs/
```

### Task 4: Search Logs with grep

```bash
# Create a fake log file
cat > ~/devops-lab/app/logs/app.log << EOF
2024-04-28 INFO App started on port 8080
2024-04-28 ERROR Cannot connect to database
2024-04-28 INFO Request received from 192.168.1.1
2024-04-28 ERROR Config file not found at /etc/app/config
2024-04-28 INFO Health check passed
EOF

# Search for errors
grep "ERROR" ~/devops-lab/app/logs/app.log

# Search case-insensitively
grep -i "error" ~/devops-lab/app/logs/app.log

# Show line numbers
grep -n "ERROR" ~/devops-lab/app/logs/app.log
```

---

## 7. Incident: App Cannot Find Config File

### Problem

The application crashed with this error:

```
ERROR: Config file not found at /etc/myapp/config.conf
```

The developer says the config file exists but the app still fails.

### Investigation

**Step 1:** Check if the file actually exists

```bash
ls /etc/myapp/
```

Output:

```
ls: cannot access '/etc/myapp/': No such file or directory
```

The directory does not exist at all.

**Step 2:** Search where the config file really is

```bash
find / -name "config.conf" 2>/dev/null
```

Output:

```
/home/taimoor/myapp/config.conf
```

The file is in `/home/taimoor/myapp/` — not in `/etc/myapp/`.

**Step 3:** Check what path the app is using

```bash
cat /home/taimoor/myapp/app.py | grep "config"
```

Output:

```python
config_path = "/etc/myapp/config.conf"   # Wrong path!
```

### Fix

**Option A:** Move the config to the expected path

```bash
sudo mkdir -p /etc/myapp
sudo cp /home/taimoor/myapp/config.conf /etc/myapp/config.conf
```

**Option B:** Fix the path in the application code

```python
config_path = "/home/taimoor/myapp/config.conf"   # Correct path
```

**Step 4:** Verify the fix

```bash
ls -l /etc/myapp/config.conf
cat /etc/myapp/config.conf
```

Restart the app and confirm it starts successfully.

### Lesson Learned

> Always verify file paths using `ls`, `find`, and `cat` before assuming a file is missing.  
> Wrong paths are one of the most common causes of application failures in production.

---

## Summary

| Command | Purpose |
|---------|---------|
| `ls`    | List files and directories |
| `cd`    | Navigate directories |
| `pwd`   | Show current directory |
| `cp`    | Copy files/directories |
| `mv`    | Move or rename files |
| `rm`    | Delete files/directories |
| `cat`   | Display file contents |
| `less`  | View large files page by page |
| `grep`  | Search text inside files |
| `chmod` | Change file permissions |
| `find`  | Search for files by name |
