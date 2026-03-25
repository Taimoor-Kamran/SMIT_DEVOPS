# 📘 DevOps — Class 1 Notes

(WSL Installation + Basic Linux Commands)

## 🚀 What is DevOps? (Brief Theory)

DevOps is a combination of Development and Operations.

Development → Creating applications or software.
Operations → Deploying and managing applications on servers.

DevOps helps teams automate software delivery so applications can be built, tested, and deployed faster and more reliably.

# 💻 Why We Install Ubuntu (WSL)

Mostly real servers run Linux, not Windows.

So before learning cloud or DevOps tools, we need a Linux environment.

Instead of installing Linux separately, we use:

## 👉 WSL (Windows Subsystem for Linux)

WSL allows Linux to run inside Windows.

## Step 1 — Enable Windows Features

Open:

Windows Button → Search → "Turn Windows features on or off"

Enable:

✅ Virtual Machine Platform
✅ Windows Subsystem for Linux

Purpose:

1. Virtual Machine Platform → Allows virtualization support.
2. WSL → Runs Linux kernel inside Windows.

Restart system after enabling.

## Step 2 — Install WSL

Command:
wsl --install
Purpose:
Installs WSL automatically
Downloads Linux kernel
Installs default Ubuntu distribution

## Step 3 — Check Available Linux Versions

wsl --list --online
Purpose:

Shows all Linux distributions available for installation.

Example:

1. Ubuntu
2. Debian
3. Kali Linux

## Step 4 — Install Ubuntu

wsl --install -d Ubuntu
Purpose:

Installs Ubuntu Linux inside Windows.

After installation:

Create username
Create password

Now Linux terminal is ready.

# 🐧 Basic Linux Commands

### 📂 ls — List Files
ls

Purpose:
Shows files and folders in current directory.

### 📂 ls -a — Show All Files
ls -a

Purpose:
Shows hidden files also (files starting with .).

### 📂 ls -l — Detailed List
ls -l

Purpose:
Shows detailed information:

1. permissions
2. owner
3. size
4. date

### 📁 mkdir — Make Directory
mkdir foldername

Purpose:
Creates a new folder.

Example:

mkdir class1
### 📁 cd — Change Directory
cd foldername

Purpose:
Moves into another folder.

Example:

cd class1
Go Back One Folder
cd ..
### 📍 pwd — Present Working Directory
pwd

Purpose:
Shows current folder location.

Example output:

/home/student/class1
🧹 clear — Clear Terminal
clear

Purpose:
Clears terminal screen.

### 🏠 cd ~ — Go Home Directory
cd ~

Purpose:
Takes user to home folder.

### 🌍 cd / — Root Directory
cd /

Purpose:
Moves to root directory (top level of Linux system).

# 📁 Class Practice Activity

### 📌 Task

Students performed the following commands:

```bash
mkdir class1
cd class1
pwd
ls
clear
```

Goal:

Understand navigation
Create directories
Use terminal confidently