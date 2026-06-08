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
