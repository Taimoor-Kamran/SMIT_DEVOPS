
## `chmod` — Change File Permissions


chmod permissions filename

Purpose:
Changes read, write, and execute permissions.

Example:

chmod 755 script.sh
chown — Change File Owner
chown user filename

Purpose:
Changes file ownership.

## chgrp — Change Group Ownership

chgrp groupname filename

Purpose:
Changes file group owner.

## 🌐 Download Command

wget — Download Files from Internet
wget URL

Purpose:
Downloads files directly from web server.

Example:

wget https://example.com/file.zip

## 🖥️ System Information Commands

uname — System Information
uname

Shows basic system name.

### Processor Information

uname -p

### Machine Hardware

uname -m

### Network Node Name

uname -n

### Operating System

uname -o

### Kernel Version

uname -r

### Complete System Info

uname -a

Displays all system details.

## uptime

uptime

Shows:

System running time
Active users
Load average
hostname
hostname

### Displays system hostname.

IP Address
hostname -i

Shows system IP address.

## w

w

Displays currently logged-in users and their activities.

## 📘 Help Command

info
info command

Shows detailed documentation (alternative to man).

## ⚡ Productivity Commands
### alias

alias shortname="command"

Creates shortcut for commands.

Example:

alias ll="ls -l"

### unalias

unalias shortname

Removes command shortcut.

ln — Create Links
ln source linkname

Creates link between files.

## 📄 File Viewing Commands

### more

more filename

Views file page by page (forward only).

### less

less filename

Improved viewer:

Scroll up/down
Search text

Press q to exit.

## 📊 Text Processing Commands

### wc — Word Count

wc filename

Shows:

Lines
Words
Characters
sort
sort filename

Sorts file content alphabetically.

### uniq

uniq filename

Removes duplicate lines.

Find Duplicate Lines
sort name.txt | uniq -d

Shows only duplicated entries.

## 📅 Date & Calendar Commands
### cal

cal

Displays calendar.

## date

date

Shows current system date and time.

