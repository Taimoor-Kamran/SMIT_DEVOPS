---

##  What is Red Hat?

**Red Hat Enterprise Linux (RHEL)** ek Linux distribution hai jo **enterprise-level environments** ke liye design kiya gaya hai.

### Simple Explanation
- Red Hat = Companies & Servers ke liye Linux OS  
- Secure, stable aur paid support ke sath aata hai  

---

## Comparison

| OS | Type | Use Case |
|----|------|----------|
| Ubuntu | Free + Easy | Students / Beginners |
| Red Hat (RHEL) | Paid + Professional | Companies / Production |

---

## Where is Red Hat Used?

- Banks  
- Company Servers  
- Cloud Systems  
- Data Centers  

---

## Key Features of Red Hat

- High Security  
- High Stability (Less crashes)  
- Enterprise Support (Paid)  
- Package Managers: `yum` / `dnf`  

---

## Is it Free or Paid?

- **RHEL → Paid (Subscription-based)**  

### Free Alternatives (RHEL-like)
- CentOS (Old)  
- Rocky Linux  
- AlmaLinux  

---

# APT Package Manager (Debian/Ubuntu)

## Update & Upgrade

```bash
sudo apt update
sudo apt list --upgradable
sudo apt upgrade
sudo apt full-upgrade
sudo apt dist-upgrade

### Install Packages

sudo apt install nodejs
sudo apt install mysql-server

### Remove Packages

sudo apt remove mysql-server
sudo apt purge mysql*
sudo apt autoremove

### Search Packages

sudo apt search mysql
sudo apt list --installed
sudo apt list --installed | grep mysql
dpkg -l | grep mysql

###Cleanup

sudo apt clean
sudo apt autoclean

## 📦 SNAP Package Manager
###  Problem Before SNAP
```


Different Linux distros ke different package managers:

| Distro | Package Manager |
|--------|-----------------|
| Ubuntu/Debian | APT (.deb) |
| Fedora/RHEL   | DNF (.rpm) |
| Arch	| Pacman |
| OpenSUSE	| Zypper |

👉 Developer ko same software multiple formats mein banana padta tha

```bash
✅ SNAP Solution

Ek .snap package → Har distro pe kaam karta hai

🔁 Works Everywhere
Ubuntu ✅
Fedora ✅
Arch ✅
Debian ✅
OpenSUSE ✅
⚙️ SNAP vs APT
APT	SNAP
System libraries use karta hai	Sab dependencies bundle hoti hain
Lightweight	Heavy but portable
Break ho sakta hai	Stable & consistent
📲 Famous SNAP Apps
sudo snap install code --classic
sudo snap install postman
sudo snap install spotify
sudo snap install obs-studio
sudo snap install telegram-desktop
sudo snap install android-studio

---