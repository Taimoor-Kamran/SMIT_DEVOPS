# 📘 User Management in Linux (Notes)

## Switch User & Root Access

sudo su
exit
sudo su → switch to root user
exit / Ctrl + D → exit from current user

## 🔹 User Information Files

ls /etc/passwd
cat /etc/passwd
cat /etc/group
cat /etc/gshadow
/etc/passwd → user details
/etc/group → group details
/etc/gshadow → group passwords
👤 Create & Manage Users

## 🔹 Add User

sudo adduser mark

👉 Creates:

user
home directory
password (interactive)

## 🔹 Check User Info

id mark
whoami

## 🔹 Switch User

su mark
exit

## 🔹 Delete User

sudo deluser mark
sudo deluser mark --remove-home

# ⚙️ Advanced User Management (Manual)

## 🔹 Create User (Manual)

sudo useradd mark
sudo useradd -m mark
-m → create home directory

## 🔹 Modify User

sudo usermod mark -s /bin/bash
sudo usermod mark -c "Mark"
sudo usermod mark -u 1000
sudo usermod mark -g taimoor
-s → shell
-c → comment
-u → user ID
-g → primary group

## 🔹 Set Password

sudo passwd mark

## 🔹 User Expiry & Lock

sudo usermod mark -e 2026-04-03
sudo usermod -L mark
-e → expiry date
-L → lock user

## 🔹 Check Password Policy

sudo chage -l mark

## 👥 Group Management

## 🔹 Create Group

sudo groupadd mygroup

## 🔹 Add User to Group

sudo usermod mark -G mygroup
sudo usermod mark -G sudo,taimoor

## 🔹 Delete Group

sudo delgroup mygroup

## 🔹 Set Group Password

sudo gpasswd mygroup

## 📂 Custom User Creation (Advanced)

sudo useradd -u 1005 -m -d /opt/mark -e 2026-04-03 -c "MARK" -g taimoor -G sudo,taimoor -s /bin/bash mark

👉 This sets:

custom UID
home directory
expiry
groups
shell

## 🔍 Useful Checks

tail -3 /etc/passwd

## 👉 Shows recently created users