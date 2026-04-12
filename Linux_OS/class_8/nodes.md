# Shell Scripting

## Main Types of Shells (Commonly Used)

### 1. Bourne Shell (sh)
- Path: `/bin/sh`

### 2. Bash (Bourne Again Shell)
- Path: `/bin/bash`

### 3. C Shell (csh)
- Uses C programming language style syntax.

### 4. TCSH (TENEX C Shell)
- Improved version of C Shell.

### 5. Korn Shell (ksh)
- Combines features of Bourne Shell and C Shell.

### 6. Z Shell (zsh)
- Advanced shell with plugins, themes, and auto-completion.

# Basic Concepts Covered

- Comments  
- Variables  
- User Input  

---

# Comments

# This is a comment

Used to write notes in scripts.

## Variables

```bash
name="Taimoor"
echo $name
## Output:
Taimoor
```

## User Input

```bash
read -p "Enter your name: " name
echo "Hello $name"
```

## Common Special Exit Values

| Code | Meaning |
|------|---------|
| 0 | Success |
| 1 | General Error / False |
| 2 | Misuse of Command |
| 126 | Command Found but Not Executable |
| 127 | Command Not Found |
| 130 | Interrupted using `Ctrl + C` |
| 255 | Out of Range / Severe Failure |

## Check Exit Status

```bash
ls
echo $?
```
- $? stores the exit status of the last command.

## Logical AND (&&)

Runs next command only if previous command is successful.

```bash
mkdir test && cd test && touch file.txt
```

## Example:

```bash
[ -e script.sh ] && echo "File Exists"
```

## Logical OR (||)

Runs second command if first command fails.

```bash
mkdir saylani || echo "Folder Already Exists"
```

```bash
ping -c 1 google.com || echo "Internet Down"
```

## Comparison Operators