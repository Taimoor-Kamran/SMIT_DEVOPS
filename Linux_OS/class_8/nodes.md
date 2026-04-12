# Main Types of Shells (Commonly Used)

## 1. Bourne Shell (sh)
- Path: `/bin/sh`

## 2. Bash (Bourne Again Shell)
- Path: `/bin/bash`

## 3. C Shell (csh)
- C programming language style shell syntax

## 4. TCSH (TENEX C Shell)

## 5. Korn Shell (ksh)

## 6. Z Shell (zsh)

---

# Basic Concepts Covered

- Comments
- Variables
- User Input

---

# Common Special Exit Values

| Code | Meaning |
|------|---------|
| 0 | Success |
| 1 | General Error / False |
| 2 | Misuse of Command |
| 126 | Command Found but Not Executable |
| 127 | Command Not Found |
| 130 | Ctrl + C |
| 255 | Out of Range / Severe Failure |

---

# Logical AND (`&&`)

```bash
[ Condition ] && [ Condition ] && Command

Example:
[ -e script.sh ] && [ 10 -eq 20 ] && echo "Success"
Comparison Operators
Numeric Operators
Operator	Meaning
-eq	Equal
-ne	Not Equal
-gt	Greater Than
-lt	Less Than
-ge	Greater Than or Equal
-le	Less Than or Equal
File Test Operators
Operator	Meaning
-d	Directory Check
-e	File or Directory Exists
-f	Regular File
-r	Readable
-w	Writable
-x	Executable
-s	File Size Greater Than 0
String Operators
Operator	Meaning
=	Equal
!=	Not Equal
Unary Operators
Operator	Meaning
-z	Empty String
-n	Not Empty String
Logical OR (||)
google.com || echo "Internet Down"
mkdir saylani || echo "Folder Already Exists"
Reserved Variable
$?
Stores last command exit status
Brace Expansion
echo file{1,2,3}
Output:
file1 file2 file3
Wildcards
* → Any Number of Characters
H*

Shows all files/directories starting with H

-d H*

Shows directories starting with H

? → Exactly One Character
Example:
file?

Matches:

file1
fileA
fileX