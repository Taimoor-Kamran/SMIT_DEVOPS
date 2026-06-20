# Class 3: Advanced Bash — Loops, Conditionals and Cron

---

## Table of Contents
1. [Conditionals](#conditionals)
2. [Loops](#loops)
3. [Cron — Scheduled Tasks](#cron--scheduled-tasks)
4. [Lab: Monitoring Script with Alerts](#lab-monitoring-script-with-alerts)
5. [Incident: Logic Bug Causes False Alerts](#incident-logic-bug-causes-false-alerts)
6. [Quick Reference](#quick-reference)

---

## Conditionals

A **conditional** lets your script make a decision — run one block of code if something is true, and a different block if it is false.
Think of it like a fork in the road: your script chooses which path to take based on a condition.

### if / elif / else

```bash
if [ condition ]; then
    # runs when condition is TRUE
elif [ other_condition ]; then
    # runs when other_condition is TRUE
else
    # runs when ALL conditions are FALSE
fi                          # "fi" closes the if block (if spelled backwards)
```

**Beginner Example — check a number:**
```bash
#!/bin/bash
score=75

if [ $score -ge 90 ]; then
    echo "Grade: A"
elif [ $score -ge 75 ]; then
    echo "Grade: B"
elif [ $score -ge 60 ]; then
    echo "Grade: C"
else
    echo "Grade: F — Please study harder!"
fi
```

Output: `Grade: B`

### Test Operators — Number Comparisons

| Operator | Meaning | Example |
|----------|---------|---------|
| `-eq` | equal to | `[ $a -eq $b ]` |
| `-ne` | not equal to | `[ $a -ne $b ]` |
| `-gt` | greater than | `[ $a -gt 10 ]` |
| `-lt` | less than | `[ $a -lt 10 ]` |
| `-ge` | greater than or equal | `[ $a -ge 10 ]` |
| `-le` | less than or equal | `[ $a -le 10 ]` |

### Test Operators — String Comparisons

| Operator | Meaning | Example |
|----------|---------|---------|
| `=` | strings are equal | `[ "$a" = "$b" ]` |
| `!=` | strings are NOT equal | `[ "$a" != "$b" ]` |
| `-z` | string is empty | `[ -z "$a" ]` |
| `-n` | string is NOT empty | `[ -n "$a" ]` |

### Test Operators — File Checks

| Operator | Meaning |
|----------|---------|
| `-f file` | file exists and is a regular file |
| `-d dir` | directory exists |
| `-e path` | file OR directory exists |
| `-r file` | file is readable |
| `-w file` | file is writable |
| `-x file` | file is executable |
| `-s file` | file exists and is NOT empty |

```bash
#!/bin/bash
LOG="/var/log/app.log"

if [ -f "$LOG" ]; then
    echo "Log file exists."
else
    echo "Log file missing — creating it..."
    touch "$LOG"
fi
```

### Combining Conditions with AND / OR

```bash
# AND — both conditions must be true
if [ $age -ge 18 ] && [ $age -le 60 ]; then
    echo "Working age"
fi

# OR — at least one condition must be true
if [ "$status" = "error" ] || [ "$status" = "critical" ]; then
    echo "ALERT: Problem detected!"
fi
```

> **Important:** Always put variables in **double quotes** inside `[ ]`
> to avoid errors when the variable is empty: `"$var"` not `$var`
