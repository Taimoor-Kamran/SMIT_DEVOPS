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

### case Statement (clean alternative to long if/elif chains)

```bash
#!/bin/bash
day="Monday"

case "$day" in
    Monday|Tuesday|Wednesday|Thursday|Friday)
        echo "$day is a weekday."
        ;;
    Saturday|Sunday)
        echo "$day is a weekend!"
        ;;
    *)
        echo "Unknown day: $day"
        ;;
esac
```

> Use `case` when you are matching one variable against many possible values.
> It is cleaner and faster to read than a long chain of `elif`.

---

## Loops

A **loop** repeats a block of code multiple times.
Instead of writing the same command 100 times, write it once inside a loop and let it run 100 times automatically.

Bash has three types of loops: `for`, `while`, and `until`.

### for Loop — loop over a list

**Syntax:**
```bash
for variable in list; do
    # commands using $variable
done
```

**Example 1 — loop over words:**
```bash
for fruit in apple banana mango orange; do
    echo "I like $fruit"
done
```
Output:
```
I like apple
I like banana
I like mango
I like orange
```

**Example 2 — loop over a number range:**
```bash
for i in {1..5}; do
    echo "Count: $i"
done
```
Output: `Count: 1` through `Count: 5`

**Example 3 — C-style for loop (like other programming languages):**
```bash
for (( i=1; i<=5; i++ )); do
    echo "Number: $i"
done
```

**Example 4 — loop over files in a directory:**
```bash
for file in /var/log/*.log; do
    echo "Found log: $file"
done
```
