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

### while Loop — keep looping while condition is TRUE

**Syntax:**
```bash
while [ condition ]; do
    # commands
done
```

**Example 1 — countdown:**
```bash
count=5
while [ $count -gt 0 ]; do
    echo "Countdown: $count"
    count=$(( count - 1 ))    # decrease count by 1
done
echo "Go!"
```

**Example 2 — wait until a service is up (real DevOps use case):**
```bash
#!/bin/bash
echo "Waiting for nginx to start..."
while ! systemctl is-active --quiet nginx; do
    echo "  nginx not ready yet... retrying in 3s"
    sleep 3
done
echo "nginx is UP!"
```

### until Loop — keep looping until condition becomes TRUE

`until` is the **opposite** of `while` — it loops as long as the condition is FALSE and stops when it becomes TRUE.

```bash
count=1
until [ $count -gt 5 ]; do
    echo "Step $count"
    count=$(( count + 1 ))
done
```
Output: `Step 1` through `Step 5`

### Loop Control — break and continue

| Command | What it does |
|---------|-------------|
| `break` | Exit the loop immediately — stop all iterations |
| `continue` | Skip the rest of THIS iteration — jump to next one |

```bash
# break example — stop when we find the number 3
for i in {1..10}; do
    if [ $i -eq 3 ]; then
        echo "Found 3, stopping."
        break
    fi
    echo "i = $i"
done
# prints: i=1, i=2, Found 3 stopping.

# continue example — skip even numbers
for i in {1..6}; do
    if [ $(( i % 2 )) -eq 0 ]; then
        continue      # skip even numbers
    fi
    echo "Odd number: $i"
done
# prints: 1, 3, 5
```

---

## Cron — Scheduled Tasks

**Cron** is a Linux tool that runs commands or scripts automatically at a scheduled time — every minute, every hour, every day, every week, etc.

Think of it like an alarm clock for your server — set it once and it runs forever on schedule.

### Crontab Syntax

A cron job is one line with **5 time fields** followed by the command:

```
* * * * *  command-to-run
│ │ │ │ │
│ │ │ │ └─── Day of week  (0-7, 0 and 7 = Sunday)
│ │ │ └───── Month        (1-12)
│ │ └─────── Day of month (1-31)
│ └───────── Hour         (0-23)
└─────────── Minute       (0-59)
```

> `*` means "every" — `* * * * *` means "every minute of every hour of every day"

### Cron Schedule Examples

| Cron expression | When it runs |
|----------------|-------------|
| `* * * * *` | Every minute |
| `0 * * * *` | Every hour (at minute 0) |
| `0 9 * * *` | Every day at 9:00 AM |
| `0 9 * * 1` | Every Monday at 9:00 AM |
| `0 0 1 * *` | First day of every month at midnight |
| `*/5 * * * *` | Every 5 minutes |
| `0 9,18 * * *` | At 9 AM and 6 PM every day |
| `0 9-17 * * 1-5` | Every hour 9AM–5PM, Monday to Friday |
| `@reboot` | Once when the server starts |
| `@daily` | Once every day at midnight |

### How to Add a Cron Job

```bash
crontab -e          # open your crontab file to edit
crontab -l          # list all your current cron jobs
crontab -r          # remove ALL your cron jobs (careful!)
```

Inside `crontab -e`, add one line per job:
```bash
# Run backup script every day at 2 AM
0 2 * * * /home/taimoor/scripts/backup.sh

# Run monitor script every 5 minutes and log output
*/5 * * * * /home/taimoor/scripts/monitor.sh >> /var/log/monitor.log 2>&1

# Start nginx when server reboots
@reboot sudo systemctl start nginx
```

> `>> /var/log/monitor.log 2>&1` means:
> - `>>` append output to the log file (don't overwrite)
> - `2>&1` also capture error messages into the same file

### Common Cron Mistakes for Beginners

| Mistake | Fix |
|---------|-----|
| Script runs manually but not from cron | Use full absolute paths: `/home/user/script.sh` not `./script.sh` |
| No output — can't see if it ran | Redirect output: `script.sh >> /tmp/cron.log 2>&1` |
| Environment variables missing | Set them inside the script or at top of crontab |
| Script not executable | Run `chmod +x script.sh` |
| Cron not running at all | Check `systemctl status cron` |

---

## Lab: Monitoring Script with Alerts

In this lab we build a real monitoring script that:
- checks CPU usage every few seconds in a loop
- checks disk usage
- checks if a service is running
- sends an **alert** to a log file if anything is above the threshold
- optionally sends an email alert

### Step 1 — Understand the tools we will use

| Tool / Command | What it does |
|---------------|-------------|
| `df -h` | Show disk usage in human-readable format |
| `top -bn1` | Run `top` once (batch mode) and output stats |
| `awk` | Extract a specific column from command output |
| `systemctl is-active` | Check if a service is running |
| `mail -s` | Send an email from terminal |
| `echo >> file` | Append a line to a log file |
| `date` | Get current date and time |

### Step 2 — Get system metrics (building blocks)

**Get disk usage percent:**
```bash
disk_usage=$(df / | awk 'NR==2 {print $5}' | tr -d '%')
echo "Disk used: $disk_usage%"
# df /         → show disk info for root partition
# awk NR==2    → pick the second line (the data line)
# print $5     → pick the 5th column (the % used)
# tr -d '%'    → remove the % sign so we get a plain number
```

**Get CPU usage percent:**
```bash
cpu_usage=$(top -bn1 | grep "Cpu(s)" | awk '{print $2}' | tr -d '%us,')
echo "CPU used: $cpu_usage%"
```

**Check if a service is running:**
```bash
if systemctl is-active --quiet nginx; then
    echo "nginx is running"
else
    echo "nginx is DOWN"
fi
```

### Step 3 — The Alert Function

Instead of repeating alert code everywhere, we write it as a **function**:

```bash
# ── alert function ──────────────────────────────────────────
LOG_FILE="/var/log/monitor_alerts.log"
ALERT_EMAIL="admin@example.com"

send_alert() {
    local message="$1"                          # $1 = first argument passed to function
    local timestamp=$(date "+%Y-%m-%d %H:%M:%S")
    local full_msg="[$timestamp] ALERT: $message"

    # Write to log file
    echo "$full_msg" >> "$LOG_FILE"

    # Print to terminal
    echo "$full_msg"

    # Send email (requires mailutils installed: sudo apt install mailutils)
    echo "$full_msg" | mail -s "Server Alert" "$ALERT_EMAIL"
}
```

> **What is a function?**
> A function is a named block of reusable code.
> You define it once and call it by name anywhere in the script.
> `send_alert "Disk is full"` calls the function and passes the message as `$1`.

### Step 4 — Complete monitor.sh Script

```bash
#!/bin/bash
# monitor.sh — system monitoring script with alerts

set -e

# ── Config ──────────────────────────────────────────────────
LOG_FILE="/var/log/monitor_alerts.log"
ALERT_EMAIL="admin@example.com"
DISK_THRESHOLD=80       # alert if disk usage > 80%
CPU_THRESHOLD=90        # alert if CPU usage > 90%
SERVICE="nginx"         # service to monitor

# ── Alert Function ───────────────────────────────────────────
send_alert() {
    local message="$1"
    local timestamp=$(date "+%Y-%m-%d %H:%M:%S")
    local full_msg="[$timestamp] ALERT: $message"
    echo "$full_msg" | tee -a "$LOG_FILE"
    echo "$full_msg" | mail -s "Server Alert" "$ALERT_EMAIL" 2>/dev/null || true
}

# ── Check Disk Usage ─────────────────────────────────────────
check_disk() {
    local usage=$(df / | awk 'NR==2 {print $5}' | tr -d '%')
    echo "Disk usage: ${usage}%"
    if [ "$usage" -gt "$DISK_THRESHOLD" ]; then
        send_alert "Disk usage is ${usage}% — threshold is ${DISK_THRESHOLD}%"
    fi
}

# ── Check CPU Usage ──────────────────────────────────────────
check_cpu() {
    local usage=$(top -bn1 | grep "Cpu(s)" | awk '{print $2}' | cut -d. -f1)
    echo "CPU usage: ${usage}%"
    if [ "$usage" -gt "$CPU_THRESHOLD" ]; then
        send_alert "CPU usage is ${usage}% — threshold is ${CPU_THRESHOLD}%"
    fi
}

# ── Check Service ────────────────────────────────────────────
check_service() {
    if systemctl is-active --quiet "$SERVICE"; then
        echo "Service $SERVICE: running"
    else
        send_alert "Service $SERVICE is DOWN!"
    fi
}

# ── Main Loop ────────────────────────────────────────────────
echo "Starting monitor... (Press Ctrl+C to stop)"
while true; do
    echo "--- Check at $(date '+%H:%M:%S') ---"
    check_disk
    check_cpu
    check_service
    sleep 30        # wait 30 seconds before next check
done
```

**How to run it:**
```bash
chmod +x monitor.sh
./monitor.sh                            # run manually
./monitor.sh >> /var/log/monitor.log &  # run in background
```

**Schedule it with cron to run every 5 minutes:**
```bash
crontab -e
# add this line:
*/5 * * * * /home/taimoor/scripts/monitor.sh >> /var/log/monitor.log 2>&1
```

---

## Incident: Logic Bug Causes False Alerts

**What happened:**
The monitoring script started sending alert emails every 30 seconds saying the disk was critically full — but when the team checked the server, disk usage was only 45%. The script had a **logic bug** in the condition that caused it to always trigger alerts even when everything was fine.

### The Buggy Code

```bash
# BUGGY version of check_disk()
check_disk() {
    local usage=$(df / | awk 'NR==2 {print $5}' | tr -d '%')
    echo "Disk usage: ${usage}%"

    if [ "$usage" -gt "$DISK_THRESHOLD" ] || [ "$usage" -lt 0 ]; then   # ← BUG HERE
        send_alert "Disk usage is ${usage}% — threshold is ${DISK_THRESHOLD}%"
    fi
}
```

**What is wrong?**
The condition uses `||` (OR) instead of `&&` (AND).

```
Buggy logic:  alert if (usage > 80) OR (usage < 0)
Correct logic: alert if (usage > 80)
```

Because `usage < 0` is impossible for disk usage, the OR condition was changed in intent by a developer who meant to add a safety check — but instead the whole condition broke in a different way.

Even worse, on some systems `df` returns a string like `45%` and if `tr -d '%'` fails silently, `$usage` becomes an empty string. Then:
- `[ "" -gt 80 ]` → error (treated as 0, which is NOT greater than 80) → OK
- `[ "" -lt 0 ]` → error (treated as 0, which IS less than 0 in some Bash versions) → ALERT!

This is the real root cause — **comparing an empty or non-numeric variable** with `-lt`.

### The Fix

```bash
# FIXED version of check_disk()
check_disk() {
    local usage=$(df / | awk 'NR==2 {print $5}' | tr -d '%')

    # Guard: make sure usage is a valid number before comparing
    if ! [[ "$usage" =~ ^[0-9]+$ ]]; then
        send_alert "Could not read disk usage — got: '$usage'"
        return 1
    fi

    echo "Disk usage: ${usage}%"

    # Fixed condition: only alert when usage is genuinely above threshold
    if [ "$usage" -gt "$DISK_THRESHOLD" ]; then
        send_alert "Disk usage is ${usage}% — threshold is ${DISK_THRESHOLD}%"
    fi
}
```

**What changed:**
1. Added a **regex check** `[[ "$usage" =~ ^[0-9]+$ ]]` to verify the value is a pure number before comparing it — this prevents the empty-string comparison bug
2. Removed the incorrect `|| [ "$usage" -lt 0 ]` condition
3. If the value is not a number, we alert about the data problem itself — not a false disk alert
