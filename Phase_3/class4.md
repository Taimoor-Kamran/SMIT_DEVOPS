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
