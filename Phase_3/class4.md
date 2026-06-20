# Class 4: Python for DevOps — Files, JSON and APIs

---

## Table of Contents
1. [Why Python for DevOps?](#why-python-for-devops)
2. [Working with Files](#working-with-files)
3. [Working with JSON](#working-with-json)
4. [Making API Requests](#making-api-requests)
5. [Lab: Parse Logs, Call API, Generate Report](#lab-parse-logs-call-api-generate-report)
6. [Incident: API Timeout and Format Error](#incident-api-timeout-and-format-error)
7. [Quick Reference](#quick-reference)

---

## Why Python for DevOps?

Bash scripts are great for simple tasks, but Python is used when things get more complex.
Python can read files, parse JSON, call APIs, send emails, generate reports and more — all in clean, readable code.

| Use Case | Bash | Python |
|----------|------|--------|
| Run system commands | ✅ Best choice | ✅ Can do with `subprocess` |
| Read / write files | ✅ Simple | ✅ More powerful |
| Parse JSON data | ❌ Very painful | ✅ Built-in `json` module |
| Call REST APIs | ⚠️ Possible with curl | ✅ Easy with `requests` |
| Generate HTML/PDF reports | ❌ Not practical | ✅ Many libraries |
| Complex logic / data processing | ⚠️ Gets messy fast | ✅ Clean and readable |

### Setting Up Python

```bash
python3 --version          # check if Python is installed
sudo apt install python3   # install on Ubuntu/Debian
sudo apt install python3-pip  # install pip (Python package manager)

pip3 install requests      # install the requests library (needed for APIs)
```

**Your first Python script:**
```python
# hello.py
print("Hello, DevOps World!")
```
```bash
python3 hello.py           # run it
```

---

## Working with Files

In DevOps, files are everywhere — log files, config files, reports, CSV data files.
Python makes reading and writing files very simple.

### File Modes

| Mode | Meaning |
|------|---------|
| `"r"` | Read — open file for reading (default) |
| `"w"` | Write — create or overwrite the file |
| `"a"` | Append — add to the end without deleting existing content |
| `"r+"` | Read and Write |

### Reading a File

```python
# Method 1 — read entire file as one big string
with open("app.log", "r") as f:
    content = f.read()
    print(content)

# Method 2 — read line by line (best for large files)
with open("app.log", "r") as f:
    for line in f:
        print(line.strip())    # .strip() removes the \n at end of each line

# Method 3 — read all lines into a list
with open("app.log", "r") as f:
    lines = f.readlines()     # returns ["line1\n", "line2\n", ...]
    print(f"Total lines: {len(lines)}")
```

> **What is `with open(...) as f`?**
> This is called a **context manager**. It automatically closes the file
> when the `with` block finishes — even if an error happens.
> Always use `with open` instead of `f = open()` to avoid leaving files open.
