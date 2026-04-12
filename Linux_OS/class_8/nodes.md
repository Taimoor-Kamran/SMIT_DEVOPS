# Shell Scripting

## Main Types of Shells (Commonly Used)

### 1. Bourne Shell (sh)
### 2. Bash (Bourne Again Shell)
### 3. C Shell (csh)
### 4. TCSH (TENEX C Shell)
### 5. Korn Shell (ksh)
### 6. Z Shell (zsh)

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
| 130 | Interrupted using `Ctrl + C` |
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
-d	Checks if it is a directory
-e	Checks if file or directory exists
-f	Checks if it is a regular file
-r	Checks if readable
-w	Checks if writable
-x	Checks if executable
-s	Checks if file size is greater than 0
String Operators
Operator	Meaning
=	Equal
!=	Not Equal
Unary Operators
Operator	Meaning
-z	String is empty
-n	String is not empty
Logical OR (||)
google.com || echo "Internet Down"
mkdir saylani || echo "Folder Already Exists"
Reserved Variable
$?
Stores the exit status of the last command.
Brace Expansion
echo file{1,2,3}
Output:
file1 file2 file3
Wildcards
* → Matches Any Number of Characters
H*

Shows all files and directories starting with H

-d H*

Shows directories starting with H

? → Matches Exactly One Character
Example:
file?
Matches:
file1
fileA
fileX