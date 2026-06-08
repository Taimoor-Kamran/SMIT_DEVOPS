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
