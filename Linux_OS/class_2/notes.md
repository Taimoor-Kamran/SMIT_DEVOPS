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

---

# 🐧 Basic Linux Commands

## 📍 `pwd` — Present Working Directory

pwd

Purpose:
Shows the current directory path where you are working.

Example Output:

/home/student


## 📂 ls — List Files
ls

Purpose:
Displays files and folders in the current directory.

## 📁 mkdir — Make Directory

mkdir foldername

Purpose:
Creates a new directory (folder).

Example:

mkdir class1

## 📁 cd — Change Directory

cd foldername

Purpose:
Moves into another directory.

Examples:

1. cd class1
2. cd ..
3. cd ~
4. cd /
4. cd -

## 📄 touch — Create File

touch filename

Purpose:
Creates an empty file.

Example:

touch notes.txt

## 📖 cat — Show File Content

cat filename

Purpose:
Displays file content inside terminal.

Example:

cat notes.txt

## 📘 man — Manual Command Help

man command

Purpose:
Shows detailed documentation of any command.

Example:

man ls

Press q to exit manual page.

## 📋 cp — Copy Files

cp source destination

Purpose:
Copies files or directories.

Example:

cp file1.txt file2.txt

## ✏️ mv — Move or Rename Files

mv oldname newname

Purpose:
Moves or renames files.

Example:

mv file1.txt newfile.txt

## ❌ rm — Remove File

rm filename

Purpose:
Deletes a file.

Example:

rm notes.txt

⚠️ Deleted files cannot be recovered easily.

## 🗑️ rmdir — Remove Empty Directory

rmdir foldername

Purpose:
Deletes an empty directory.

## 🔍 grep — Search Text

grep "word" filename

Purpose:
Searches specific text inside a file.

Example:

grep hello notes.txt

## 🕘 history — Command History

history

Purpose:
Shows previously executed commands.

## 🧹 clear — Clear Terminal

clear

Purpose:
Clears terminal screen.

## 🗣️ echo — Print Text

echo "text"

Purpose:
Displays text output in terminal.

Example:

echo "Hello DevOps"

## 📊 stat — File Information

stat filename

Purpose:
Shows detailed file information such as:

1. File size
2. Permissions
3. Created date
4. Modified date

Example:

stat notes.txt

## ls -a Show All Files

ls -a

Purpose:
Displays all files including hidden files.

Hidden files in Linux start with a dot (.).

Example Hidden Files:

.bashrc
.git
.env

✅ Useful for viewing configuration files.

## 📋 ls -l — Long Listing Format

ls -l

Purpose:
Shows detailed information about files and folders.

Displays:

File permissions
Owner name
File size
Date & time
File name

Example Output:

-rw-r--r-- 1 user user 1200 Mar 20 notes.txt

## 🌳 ls -R — Recursive Listing

ls -R

Purpose:
Lists all files and folders recursively, including subdirectories.

This command shows the complete folder structure.

✅ Useful for understanding project directory structure.

## ⭐ Combined Example

You can combine options:

ls -la

Shows:

Hidden files
Detailed list

---
