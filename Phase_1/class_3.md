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

