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

### Writing and Appending to a File

```python
# Write — creates the file (or wipes it if it already exists)
with open("report.txt", "w") as f:
    f.write("=== Daily Report ===\n")
    f.write("Status: OK\n")

# Append — adds to the end of the file
with open("report.txt", "a") as f:
    f.write("New line added\n")

# Write multiple lines at once
lines = ["Server: OK\n", "DB: OK\n", "API: OK\n"]
with open("report.txt", "w") as f:
    f.writelines(lines)
```

### Useful File Operations with os and pathlib

```python
import os

os.path.exists("app.log")        # True if file exists
os.path.getsize("app.log")       # file size in bytes
os.makedirs("logs/2026", exist_ok=True)   # create nested folders
os.listdir("/var/log")           # list files in a directory
os.remove("old.log")             # delete a file
os.rename("old.log", "new.log")  # rename a file
```

```python
# Searching for ERROR lines in a log file
with open("/var/log/app.log", "r") as f:
    errors = [line for line in f if "ERROR" in line]

print(f"Found {len(errors)} errors")
for err in errors:
    print(err.strip())
```

---

## Working with JSON

**JSON** (JavaScript Object Notation) is the most common format for exchanging data between systems — APIs, config files, and log files all use JSON.

### What JSON Looks Like

```json
{
  "server": "web-01",
  "status": "running",
  "cpu": 45,
  "disk": 72,
  "services": ["nginx", "mysql", "redis"],
  "alerts": []
}
```

JSON has these data types:

| JSON type | Python equivalent | Example |
|-----------|-------------------|---------|
| string | `str` | `"hello"` |
| number | `int` or `float` | `42`, `3.14` |
| boolean | `bool` | `true` → `True` |
| null | `None` | `null` → `None` |
| array | `list` | `[1, 2, 3]` |
| object | `dict` | `{"key": "value"}` |

### Parsing JSON (string → Python object)

```python
import json

# JSON string (what an API sends you)
json_string = '{"server": "web-01", "cpu": 45, "status": "running"}'

# Parse it into a Python dictionary
data = json.loads(json_string)     # loads = load from String

print(data["server"])    # web-01
print(data["cpu"])       # 45
print(data["status"])    # running
print(type(data))        # <class 'dict'>
```

> `json.loads()` — converts a **JSON string** into a Python **dictionary**
> Think: "loads" = load from String

### Reading JSON from a File

```python
import json

# Read a JSON config file
with open("config.json", "r") as f:
    config = json.load(f)        # load = load from File (no 's')

print(config["server"])
print(config["services"])        # ['nginx', 'mysql', 'redis']

# Loop over a JSON list
for service in config["services"]:
    print(f"Service: {service}")
```

> `json.load()` (no 's') — reads JSON directly **from a file object**
> `json.loads()` (with 's') — parses a JSON **string**

### Writing JSON to a File

```python
import json

report = {
    "date": "2026-06-20",
    "server": "web-01",
    "cpu_usage": 45,
    "disk_usage": 72,
    "alerts": ["Disk above 70%"],
    "status": "warning"
}

# Write Python dict → JSON file
with open("report.json", "w") as f:
    json.dump(report, f, indent=4)    # indent=4 makes it human-readable

# Convert Python dict → JSON string (useful for printing or sending)
json_string = json.dumps(report, indent=4)
print(json_string)
```

> `json.dump()` — write to **file**
> `json.dumps()` — convert to **string** (s = string)

---

## Making API Requests

An **API** (Application Programming Interface) lets your script talk to other services over the internet.
For example: get weather data, post a Slack message, query a monitoring system, or trigger a deployment.

APIs use **HTTP** — the same protocol your browser uses to load websites.

### HTTP Methods — the 4 main ones

| Method | What it does | Real example |
|--------|-------------|-------------|
| `GET` | Fetch / read data | Get server status |
| `POST` | Send / create data | Send an alert message |
| `PUT` | Update existing data | Update a config |
| `DELETE` | Delete data | Remove an old deployment |

### HTTP Status Codes

| Code | Meaning |
|------|---------|
| `200` | OK — success |
| `201` | Created — new resource created |
| `400` | Bad Request — your request had an error |
| `401` | Unauthorized — wrong or missing API key |
| `404` | Not Found — the URL does not exist |
| `429` | Too Many Requests — you are being rate-limited |
| `500` | Internal Server Error — the API server crashed |
| `503` | Service Unavailable — server is down |

### GET Request — Fetch Data from an API

```python
import requests

# Simple GET request
response = requests.get("https://api.github.com/users/Taimoor-Kamran")

print(response.status_code)      # 200 = success
print(response.json())           # parsed JSON response as Python dict

# Access specific fields
data = response.json()
print(data["login"])             # GitHub username
print(data["public_repos"])      # number of public repos
print(data["followers"])         # number of followers
```
