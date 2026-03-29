# 🔎 File Searching Commands

## 📍 `locate`

locate filename

Purpose:
Quickly finds files using a prebuilt database.

✅ Faster than find.

## 📍 find

find /path -name filename

Purpose:
Searches files in real-time directories.

Example:

find /home -name notes.txt

## 🔍 Text Searching with grep

grep is used to search text inside files.

Basic Search
grep "word" filename
Ignore Case
grep -i "helloworld" name.txt

### Search without case sensitivity.

Exclude Matching Lines
cat name.txt | grep -v "helloworld"

### Shows lines that DO NOT contain the word.

Recursive Search
grep -r "helloworld" .

### Search inside all files and folders.

Show Only Matched Word
grep -o "helloworld" name.txt

### Displays only matching text.

Show File Name Only
grep -l "helloworld" name.txt

### Displays filename containing match.

Count Matches
grep -c "helloworld" name.txt

Shows number of matches.

Show Line Numbers
grep -n "program" name.txt

### Displays line numbers with results.

Show Byte Offset
grep -b "program" name.txt

Shows position of match in file.

Pipe Example
cat name.txt | grep "helloworld"

Filters output using pipe (|).

## 📘 Help Commands
Manual Pages
man man

Opens manual documentation.

Press q to exit.

whatis
whatis ls

## Shows short description of command.

which
which ls

## Shows command executable path.

type
type ls

## Tells whether command is alias, builtin, or executable.

whereis
whereis ls

Shows binary, source, and manual locations.

## 🔐 Administrative Command
sudo
sudo command

Runs command with administrator privileges.

Example:

sudo apt update
💾 Disk Usage Commands
Check Disk Space
df -h

## Shows disk usage in human-readable format.

Folder Size
du -h

Displays directory size.

## 📄 File Viewing Commands
head
head filename

### Shows first 10 lines of file.

tail
tail filename

Shows last 10 lines.

Live Log Monitoring
tail -f filename

Displays file updates in real-time.

⭐ Very important for DevOps log monitoring.

## 🔄 File Comparison

diff
diff file1 file2

Compares two files and shows differences.

