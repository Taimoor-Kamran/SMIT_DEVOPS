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

read -p "Enter your name: " name
echo "Hello $name"