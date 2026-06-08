# Class 1: Bash Scripting Basics

---

## Table of Contents
1. [What is Bash Scripting?](#what-is-bash-scripting)
2. [Variables](#variables)
3. [Arguments (Args)](#arguments-args)
4. [Exit Codes](#exit-codes)
5. [Putting It All Together](#putting-it-all-together)
6. [Quick Reference](#quick-reference)

---

## What is Bash Scripting?

**Bash** stands for **Bourne Again SHell**.
It is the default terminal language on Linux and macOS.
A **Bash script** is a plain text file containing a list of terminal commands that run automatically, one by one.

**Why use Bash scripts?**
- Automate repetitive tasks (backups, deployments, cleanup)
- Save time — run 100 commands with one file
- Used heavily in DevOps, CI/CD pipelines, and server management

**Your first Bash script — Hello World:**

```bash
#!/bin/bash
# This is a comment — Bash ignores lines starting with #

echo "Hello, World!"
```

**How to run the script:**
```bash
chmod +x hello.sh    # give the file permission to execute
./hello.sh           # run it
```

> `#!/bin/bash` is called a **shebang** — it tells the system to use Bash to run this file.
> Always put it on the very first line of every script.

---

## Variables

A **variable** is a name that stores a value, so you can reuse it later.
Think of it like a labelled box — you put something inside the box and refer to it by the label.

### Syntax Rules

```bash
name="Taimoor"        # ✅ correct — no spaces around =
name = "Taimoor"      # ❌ wrong   — spaces break it in Bash
```

To **use** a variable, put `$` in front of the name:
```bash
name="Taimoor"
echo "Hello, $name"      # prints: Hello, Taimoor
echo "Hello, ${name}!"   # {} are optional but safer for complex strings
```

### Types of Variables

#### 1. String variable
```bash
city="Karachi"
echo "I live in $city"
```

#### 2. Number variable
```bash
age=20
echo "I am $age years old"
```

#### 3. Variable from a command (Command Substitution)
```bash
today=$(date)           # runs the 'date' command and stores the output
echo "Today is: $today"
```

#### 4. Readonly variable (constant — value cannot change)
```bash
readonly MAX_RETRIES=3
echo "Max retries allowed: $MAX_RETRIES"
```

#### 5. Environment variable (available to child processes)
```bash
export DATABASE_URL="localhost:5432"
echo $DATABASE_URL
```

### Common Beginner Mistakes with Variables

| Mistake | Wrong | Correct |
|---------|-------|---------|
| Space around = | `name = "Ali"` | `name="Ali"` |
| Forgot $ to read | `echo name` | `echo $name` |
| Wrong quotes | `echo '$name'` (prints literally) | `echo "$name"` |

> **Single quotes `'`** print everything literally — `$name` stays as `$name`.
> **Double quotes `"`** expand variables — `$name` becomes the value.

---

## Arguments (Args)

**Arguments** are values you pass to a script when you run it, so the script can behave differently each time without changing the code.

**Example:**
```bash
./greet.sh Taimoor        # "Taimoor" is the argument
./greet.sh Ahmed          # "Ahmed" is the argument — same script, different result
```

### Special Argument Variables

Bash gives you built-in variables to read the arguments inside your script:

| Variable | Meaning |
|----------|---------|
| `$0` | Name of the script itself |
| `$1` | First argument |
| `$2` | Second argument |
| `$3` | Third argument |
| `$@` | All arguments as separate words |
| `$#` | Total number of arguments passed |

### Real Example — greet.sh
```bash
#!/bin/bash

echo "Script name : $0"
echo "Hello, $1!"
echo "You are $2 years old."
echo "Total arguments passed: $#"
```

Run it:
```bash
./greet.sh Taimoor 20
```

Output:
```
Script name : ./greet.sh
Hello, Taimoor!
You are 20 years old.
Total arguments passed: 2
```

### Looping Over All Arguments
```bash
#!/bin/bash
for arg in "$@"; do
    echo "Argument: $arg"
done
```

Run it:
```bash
./list.sh apple banana mango
```

Output:
```
Argument: apple
Argument: banana
Argument: mango
```

### Checking If an Argument Was Passed
```bash
#!/bin/bash
if [ -z "$1" ]; then
    echo "Error: Please provide a name."
    echo "Usage: ./greet.sh <name>"
    exit 1
fi
echo "Hello, $1!"
```
> `-z` means "is this string empty?" — we will cover `exit 1` in the next section.

---

## Exit Codes

Every command in Linux/Bash, when it finishes, sends back a number to the system.
That number is called the **exit code** (also called return code or exit status).

| Exit Code | Meaning |
|-----------|---------|
| `0` | Success — the command worked perfectly |
| `1` | General error — something went wrong |
| `2` | Misuse of command (wrong syntax) |
| `126` | Permission denied — file is not executable |
| `127` | Command not found |
| `130` | Script stopped by Ctrl+C |

### Checking the Exit Code of the Last Command

After any command runs, its exit code is stored in `$?`:
```bash
ls /home               # this should succeed
echo $?                # prints: 0  (success)

ls /fake-folder        # this will fail (folder does not exist)
echo $?                # prints: 2  (error)
```

### Setting Exit Codes in Your Script

Use `exit <number>` to stop the script and report success or failure:
```bash
#!/bin/bash

if [ -z "$1" ]; then
    echo "Error: No argument given!"
    exit 1          # stop here and report failure
fi

echo "Hello, $1!"
exit 0              # stop here and report success
```

### Using Exit Codes to Chain Commands

Bash has two powerful operators that use exit codes:

| Operator | Meaning | Example |
|----------|---------|---------|
| `&&` | Run next command ONLY if previous succeeded (exit 0) | `mkdir logs && echo "Created"` |
| `\|\|` | Run next command ONLY if previous failed (non-zero) | `mkdir logs \|\| echo "Failed"` |

```bash
# Real-world example: only deploy if tests pass
npm test && ./deploy.sh
#          ^ deploy.sh only runs if npm test exits with 0

# Show error message if directory creation fails
mkdir /protected-dir || echo "Could not create directory — permission denied"
```

### set -e — Stop Script on Any Error

Add `set -e` at the top of your script to make it **stop immediately** if any command fails.
This is a best practice in DevOps scripts to avoid silently ignoring errors.

```bash
#!/bin/bash
set -e              # exit immediately if any command fails

echo "Step 1: Installing dependencies..."
npm install         # if this fails, script stops here

echo "Step 2: Running tests..."
npm test            # if this fails, script stops here

echo "Step 3: Deploying..."
./deploy.sh

echo "All done!"
```

---

## Putting It All Together

Here is a complete script that uses **variables**, **arguments**, and **exit codes** together.

**Script: backup.sh**
```bash
#!/bin/bash
set -e

# --- Variables ---
BACKUP_DIR="/tmp/backups"
TIMESTAMP=$(date +"%Y-%m-%d_%H-%M-%S")   # e.g. 2026-06-08_14-30-00

# --- Arguments ---
SOURCE=$1     # first argument: the folder to back up

# --- Validate argument ---
if [ -z "$SOURCE" ]; then
    echo "Error: Please provide a source folder."
    echo "Usage: ./backup.sh /path/to/folder"
    exit 1
fi

if [ ! -d "$SOURCE" ]; then
    echo "Error: '$SOURCE' is not a valid directory."
    exit 1
fi

# --- Do the backup ---
mkdir -p "$BACKUP_DIR"
DEST="$BACKUP_DIR/backup_${TIMESTAMP}.tar.gz"

echo "Backing up '$SOURCE' to '$DEST'..."
tar -czf "$DEST" "$SOURCE"

# --- Check if backup succeeded ---
if [ $? -eq 0 ]; then
    echo "Backup successful!"
    exit 0
else
    echo "Backup failed!"
    exit 1
fi
```

**How to run:**
```bash
chmod +x backup.sh
./backup.sh /home/taimoor/projects
```

---

## Quick Reference

### Variables Cheat Sheet

| Task | Code |
|------|------|
| Create a variable | `name="Taimoor"` |
| Read a variable | `echo $name` |
| Variable from command | `today=$(date)` |
| Readonly variable | `readonly PI=3.14` |
| Export to environment | `export PORT=8080` |
| Check if empty | `[ -z "$var" ]` |
| Check if not empty | `[ -n "$var" ]` |

### Arguments Cheat Sheet

| Variable | Value |
|----------|-------|
| `$0` | Script name |
| `$1` to `$9` | Positional arguments |
| `$@` | All arguments |
| `$#` | Number of arguments |

### Exit Codes Cheat Sheet

| Code | Meaning |
|------|---------|
| `0` | Success |
| `1` | General error |
| `127` | Command not found |
| `$?` | Exit code of last command |
| `exit 0` | Manually exit with success |
| `exit 1` | Manually exit with error |
| `set -e` | Auto-exit on any error |
| `cmd1 && cmd2` | Run cmd2 only if cmd1 succeeds |
| `cmd1 \|\| cmd2` | Run cmd2 only if cmd1 fails |

---

*End of Class 1 — Phase 3*
