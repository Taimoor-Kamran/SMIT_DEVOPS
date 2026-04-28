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

