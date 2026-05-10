# Class 6: Basic Tuning — Identifying Heavy Processes, Simulating Load & Safe Termination

---

## 1. What is Basic Tuning?

**Tuning** means adjusting a running Linux system so it uses CPU, memory, and disk as efficiently as possible.  
Basic tuning does not require rewriting code or changing hardware — it means:

- Identifying which processes consume too many resources
- Adjusting their priority so critical work gets done first
- Knowing when and how to safely stop or restart a misbehaving process

### Why Tuning Matters

| Without Tuning | With Tuning |
|----------------|-------------|
| One runaway process can freeze the whole system | Resources are distributed fairly |
| Admins react to crashes after they happen | Problems are caught before impact |
| No idea what is eating CPU or memory | Clear visibility into every process |
| Services restart incorrectly and lose data | Processes are stopped and restarted safely |

---

## 2. Key Metrics to Understand Before Tuning

Before you change anything, you must understand what you are looking at.

### Load Average

```bash
uptime
```

Output:

```
10:45:01 up 3:20, 1 user,  load average: 2.45, 1.87, 1.12
                                          ↑     ↑     ↑
                                        1 min  5 min  15 min
```

**How to interpret load average:**

```
Number of CPU cores = how much load is "normal"

1-core system:
  load 1.0  → fully loaded (acceptable)
  load 2.0  → overloaded — processes are waiting for CPU

4-core system:
  load 4.0  → fully loaded (acceptable)
  load 8.0  → overloaded — serious problem

Rule: if load average > number of CPU cores, the system is stressed
```

**Check how many CPU cores you have:**

```bash
nproc
```

Output:

```
4
```

### CPU Usage Breakdown

```bash
top
```

Header line:

```
%Cpu(s): 89.3 us,  3.2 sy,  0.0 ni,  7.0 id,  0.5 wa
          ↑         ↑         ↑         ↑         ↑
        user      system    nice      idle      I/O wait
```

| Field | Meaning | Healthy Value |
|-------|---------|---------------|
| `us` | CPU used by user programs | Depends on workload |
| `sy` | CPU used by the OS kernel | < 5% |
| `ni` | CPU used by nice'd (low-priority) processes | Varies |
| `id` | Idle — CPU doing nothing | Higher is better |
| `wa` | Waiting for disk/network I/O | < 10% |

> **Golden rule:** if `id` (idle) is near 0%, your CPU is maxed out. If `wa` is high, the bottleneck is disk or network, not CPU.

### Memory Usage

```bash
free -h
```

Output:

```
              total   used    free    shared  buff/cache  available
Mem:          3.8G    2.1G    300M    100M    1.4G        1.5G
Swap:         2.0G    50M     1.9G
```

| Field | Meaning |
|-------|---------|
| `total` | Total RAM installed |
| `used` | Currently allocated |
| `available` | What new processes can actually use |
| `Swap used` | If this is growing, RAM is full — serious problem |

---

## 3. Identifying Heavy Processes

### Method 1 — ps aux Sorted by CPU

```bash
# Show top CPU consumers — heaviest first
ps aux --sort=-%cpu | head -15
```

Output:

```
USER     PID   %CPU %MEM  COMMAND
student  3421  99.2  0.0   yes
student  3422  98.8  0.0   yes
root      856   1.2  0.5   mysqld
root        1   0.0  0.1   systemd
```

### Method 2 — ps aux Sorted by Memory

```bash
# Show top memory consumers — heaviest first
ps aux --sort=-%mem | head -15
```

### Method 3 — top (Interactive)

```bash
top
```

Inside top:

```
P     → Sort by CPU usage (press once — heaviest process jumps to top)
M     → Sort by Memory usage
k     → Kill a process (asks for PID, then signal number)
q     → Quit
```

---

## 4. Simulating Load (For Testing and Learning)

Before you can practice identifying and fixing heavy processes, you need to create some.

### Method 1 — CPU Load with yes

```bash
# What does this command mean?
# yes        → a program that prints "y" forever, as fast as possible
# /dev/null  → a trash bin — anything sent here is thrown away
# &          → runs in the background so your terminal stays free
# Together: this burns 100% of one CPU core
yes > /dev/null &

# Eat multiple cores (4 processes = 4 cores saturated)
yes > /dev/null &
yes > /dev/null &
yes > /dev/null &
yes > /dev/null &
```

---

## 5. Nice Values — Process Priority

### What is a Nice Value?

Every process has a **nice value** that tells the OS how much CPU priority to give it.

```
Nice Value Range: -20 to +19

  -20  →  Highest priority (gets CPU first — very greedy)
    0  →  Default priority (normal processes)
  +19  →  Lowest priority (gives way to everything else — very polite)
```

Think of it like a queue — a lower nice value means you skip the line.

### Why Nice Values Matter for Tuning

If a low-priority background job is consuming CPU and slowing down your web server:

- Increase the background job's nice value (make it polite)
- The web server processes get CPU time first
- The background job finishes eventually, just slower

### Start a Process with a Custom Nice Value

```bash
# Start a process with low priority (nice = 10)
nice -n 10 yes > /dev/null &

# Start a process with lowest priority
nice -n 19 yes > /dev/null &
```

### Change Nice Value of a Running Process (renice)

```bash
# First find the PID
ps aux | grep yes

# Lower its priority (make it nicer — give way to others)
sudo renice +15 -p 3421

# Raise its priority (make it greedier — gets more CPU)
sudo renice -5 -p 3421
```

> Only root can set negative nice values (priorities below 0).  
> Any user can make their own process nicer (increase nice value).

### Verify the Nice Value Changed

```bash
ps aux | grep yes
# NI column shows the current nice value

# Or in top — the NI column
top
```

### Nice Value Comparison Table

| Situation | Nice Value | Effect |
|-----------|-----------|--------|
| Web server, database | -5 to 0 | Gets CPU before others |
| Normal user apps | 0 | Default — fair share |
| Background backup | +10 | Yields CPU to important work |
| Low-priority batch job | +19 | Only runs when CPU is idle |

---

## 6. Safely Terminating and Restarting Processes

### The Right Order — Always Try SIGTERM First

```
Step 1: Send SIGTERM (-15)    → polite request to stop
         ↓ wait a few seconds
Step 2: Is the process gone?  → if yes, done
         ↓ if still running
Step 3: Send SIGKILL (-9)     → force kill, no cleanup
```

**Why this order matters:**

```
SIGTERM allows the process to:        SIGKILL forces the OS to:
✓ Save open files and data            ✗ No saving — data can be lost
✓ Close database connections          ✗ Connections left hanging
✓ Write final log entries             ✗ Logs may be incomplete
✓ Release file locks                  ✗ Locks remain — next start fails

Use SIGTERM first. Only use SIGKILL when SIGTERM fails.
```

### Terminate a Process Safely

```bash
# Step 1: Find the PID
ps aux | grep bad_process

# Step 2: Send polite stop request
kill -15 [PID]

# Step 3: Wait 5 seconds
sleep 5

# Step 4: Check if it is gone
ps aux | grep [PID]

# Step 5: If still running — force kill
kill -9 [PID]
```

### Terminate by Name

```bash
# Politely stop all processes named "bad_process"
pkill bad_process

# Force kill all processes named "bad_process"
pkill -9 bad_process

# Or with killall
killall bad_process
killall -9 bad_process
```

### Restart a Service Safely (systemd)

For services managed by systemd, **never kill the process directly in production**.  
Use `systemctl` — it handles stopping and starting cleanly.

```bash
# Safe restart — systemd sends SIGTERM, waits, then starts fresh
sudo systemctl restart nginx

# Stop only
sudo systemctl stop nginx

# Start only
sudo systemctl start nginx

# Check if it came back up
sudo systemctl status nginx
```

### Reload Config Without Full Restart (When Supported)

Some services can reload their configuration without stopping (zero downtime):

```bash
# Reload nginx config — no dropped connections
sudo systemctl reload nginx

# SIGHUP — what is it?
# HUP = "hang up". Most programs use it to reload their config file.
# It does NOT stop the process — just tells it to re-read its settings.
# Example: nginx reloads nginx.conf without dropping any connections.
kill -1 $PID
kill -SIGHUP $PID
```

---

## 7. Lab: Hands-On Practice

### Task 1: Simulate Load and Watch the System React

```bash
# Step 1: Check baseline — record current load average
uptime

# Step 2: Create 3 CPU-eating processes
yes > /dev/null &
yes > /dev/null &
yes > /dev/null &

# Step 3: Check load average again
uptime

# Step 4: Watch in real time
top
# Press P to sort by CPU
# Observe the yes processes at the top eating nearly 100% each

# Step 5: Check with ps — sorted by CPU
ps aux --sort=-%cpu | head -10
```

### Task 2: Adjust Process Priority with renice

```bash
# Start two yes processes
yes > /dev/null &
PID_A=$!
echo "PID_A: $PID_A"

yes > /dev/null &
PID_B=$!
echo "PID_B: $PID_B"

# Make PID_A high priority
sudo renice -10 -p $PID_A

# Make PID_B low priority
sudo renice +15 -p $PID_B

# Watch the difference in top
top
# Press P → you will see PID_A gets more CPU time than PID_B

# Verify nice values
ps -o pid,ni,comm -p $PID_A $PID_B
```

### Task 3: Terminate Processes Safely

```bash
# First list all yes processes
ps aux | grep yes

# Step 1: Politely terminate one
kill -15 $PID_A

# Wait and verify it is gone
sleep 2
ps aux | grep $PID_A
# Should show nothing (or only the grep command itself)

# Step 2: Force kill the other (simulating a stuck process)
kill -9 $PID_B

# Clean up everything remaining
pkill yes

# Verify none left
ps aux | grep yes
```

### Task 4: Safe Service Restart

```bash
# Install nginx if not present
sudo apt install nginx

# Start it
sudo systemctl start nginx

# Check it is running
sudo systemctl status nginx

# Simulate a config reload without downtime
sudo systemctl reload nginx

# Now do a full restart
sudo systemctl restart nginx

# Confirm it came back up correctly
sudo systemctl is-active nginx
```

---

## 8. Incident: High CPU Due to Runaway Process — Isolate and Fix

### Scenario

You are on call. At 2:00 AM an alert fires:

```
ALERT: CPU usage on web-server-01 is at 98% for the last 5 minutes
       Load average: 7.8 / 4-core system (should be <= 4.0)
       Response time spiked from 120ms to 8000ms
```

Your job is to find the cause, isolate it, and restore the system.

### Step 1: Get Immediate Situational Awareness

```bash
# Check load average and uptime
uptime
```

Output:

```
02:15:33 up 14 days, 2:10, 1 user, load average: 7.82, 6.91, 5.43
```

Load is nearly double the 4-core capacity. Something is seriously wrong.

```bash
# Check overall CPU and memory
top
```

Top header:

```
%Cpu(s): 98.7 us,  0.8 sy,  0.0 ni,  0.1 id
```

`id` (idle) is 0.1% — the CPU is completely saturated.

### Step 2: Identify the Runaway Process

```bash
# Sort by CPU — find the top consumer immediately
ps aux --sort=-%cpu | head -10
```

Output:

```
USER      PID   %CPU %MEM  COMMAND
deploy   8821  197.2  1.2  python3 data_processor.py
www-data  412    1.1  0.8  nginx: worker process
mysql    1023    0.9  3.4  mysqld
```

Found it: `data_processor.py` running as user `deploy`, consuming 197% CPU (using 2 full cores).

### Step 3: Gather More Information Before Killing

Do not kill it yet. First understand what it is doing.

```bash
# How long has it been running?
ps -p 8821 -o pid,etime,cmd
```

Output:

```
  PID     ELAPSED  CMD
 8821    02:47:13  python3 data_processor.py
```

It has been running for nearly 3 hours — far longer than expected.

```bash
# /proc is a special Linux folder with live info about every running process.
# /proc/8821/fd shows all files that process 8821 currently has open.
ls -la /proc/8821/fd | head -20

# Easier to read alternative:
lsof -p 8821
```

```bash
# Is it a scheduled job? Check crontab for deploy user
sudo crontab -u deploy -l
```

Output:

```
0 23 * * * /opt/scripts/data_processor.py
```

It is a nightly batch job that started at 23:00 but never finished. It is now stuck in a loop.

```bash
# Check system logs for errors from this process
sudo journalctl --since "2 hours ago" | grep data_processor
```

Output:

```
02:00:01 web-server-01 python3[8821]: WARNING: retry attempt 1847 — connection timeout
02:00:04 web-server-01 python3[8821]: WARNING: retry attempt 1848 — connection timeout
```

The process is stuck in an infinite retry loop — the remote database it connects to went offline, and the script has no maximum retry limit.

### Step 4: Isolate — Lower Its Priority First

Before killing, lower the process priority to reduce impact on the web server.  
This buys time to investigate and confirm your fix plan.

```bash
# Drop to lowest priority so nginx can breathe
sudo renice +19 -p 8821
```

```bash
# Verify CPU impact reduced
top
```

Top header now:

```
%Cpu(s): 61.4 us,  2.1 sy,  2.3 ni, 34.1 id
```

Idle jumped from 0.1% to 34.1%. The web server is responding again.

### Step 5: Terminate the Runaway Process Safely

```bash
# Step 1: Send SIGTERM — give it a chance to clean up
sudo kill -15 8821
```

```bash
# Step 2: Wait and check
sleep 5
ps aux | grep 8821
```

Output:

```
deploy   8821  98.2  1.2  python3 data_processor.py
```

It ignored SIGTERM — stuck in a retry loop, not checking for signals.

```bash
# Step 3: Force kill with SIGKILL
sudo kill -9 8821
```

```bash
# Step 4: Confirm it is gone
ps aux | grep 8821
# No output — process is gone
```

### Step 6: Verify System Recovery

```bash
# Check load average — should be dropping
uptime
```

Output (2 minutes later):

```
02:20:01 up 14 days, 2:15, 1 user, load average: 1.12, 3.45, 4.87
```

Load is falling. The 1-minute average is already healthy.

```bash
# Confirm nginx is still running correctly
sudo systemctl status nginx
curl -I http://localhost
```

Output:

```
HTTP/1.1 200 OK
```

Web server is responding normally.

### Step 7: Root Cause and Fix

**Root cause:** `data_processor.py` had no maximum retry limit. When the remote database went offline at 00:30, the script entered an infinite retry loop.

**Immediate fix (already done):** killed the runaway process

**Proper fix (in the code):**

```python
# Before fix — no retry limit
while True:
    try:
        connect_to_database()
        break
    except ConnectionError:
        time.sleep(10)
        continue   # retries forever

# After fix — max retries with exponential backoff
MAX_RETRIES = 5
for attempt in range(MAX_RETRIES):
    try:
        connect_to_database()
        break
    except ConnectionError:
        if attempt == MAX_RETRIES - 1:
            raise   # fail loudly after max retries
        time.sleep(10 * (2 ** attempt))   # exponential backoff
```

**Restart the job manually once the database is back online:**

```bash
# nc = netcat — a tool to test if a network port is reachable
# -z = just check if the port is open, don't send data
# -v = show the result clearly
# 5432 = the default port for PostgreSQL (our database)
nc -zv db-server 5432
# Output: Connection to db-server 5432 port [tcp/postgresql] succeeded!

# Now re-run the job safely
sudo -u deploy python3 /opt/scripts/data_processor.py
```

### Incident Summary

| Step | Action | Command Used |
|------|--------|--------------|
| 1 | Checked load average | `uptime` |
| 2 | Found CPU at 98% idle near zero | `top` |
| 3 | Identified runaway process | `ps aux --sort=-%cpu` |
| 4 | Checked how long it ran | `ps -p 8821 -o pid,etime,cmd` |
| 5 | Found root cause in logs | `journalctl` |
| 6 | Isolated with renice +19 | `sudo renice +19 -p 8821` |
| 7 | Tried SIGTERM first | `sudo kill -15 8821` |
| 8 | Used SIGKILL as last resort | `sudo kill -9 8821` |
| 9 | Verified system recovered | `uptime`, `curl` |
| 10 | Fixed the code — added retry limit | Code change deployed |

### Common Runaway Process Scenarios

| Symptom | Likely Cause | First Action |
|---------|-------------|--------------|
| CPU 100%, `id` near 0 | Infinite loop or spinning process | `ps --sort=-%cpu`, then renice and kill |
| Load average >> core count | Too many processes piled up | Find zombie or blocked processes |
| Memory keeps growing | Memory leak in long-running process | `ps --sort=-%mem`, check VmRSS in `/proc/PID/status` |
| High `wa` in top | Process doing excessive disk I/O | `iotop` to find I/O offender |
| Service becomes slow suddenly | CPU stolen by noisy neighbor process | `renice` the offender to +19 |

---

## Summary

### Tuning Workflow Checklist

```
[ ] Check load average: uptime — compare to nproc (core count)
[ ] Check CPU breakdown: top — look at "id" (idle should be > 10%)
[ ] Check memory: free -h — watch for growing swap usage
[ ] Find top consumers: ps aux --sort=-%cpu | head -10
[ ] Investigate before acting: check PID runtime, logs, crontab
[ ] Isolate first: sudo renice +19 -p [PID] — reduce impact while investigating
[ ] Terminate safely: SIGTERM first, wait, then SIGKILL only if needed
[ ] Use systemctl for services — never kill service processes directly
[ ] Verify recovery: uptime, systemctl status, curl / smoke test
[ ] Fix root cause — do not just kill the process and walk away
```

### Quick Reference — Tuning Commands

| Goal | Command |
|------|---------|
| Check load average | `uptime` |
| Find CPU hogs | `ps aux --sort=-%cpu \| head -10` |
| Find memory hogs | `ps aux --sort=-%mem \| head -10` |
| Live monitoring | `top` or `htop` |
| Simulate CPU load | `yes > /dev/null &` |
| Lower process priority | `sudo renice +15 -p [PID]` |
| Raise process priority | `sudo renice -5 -p [PID]` |
| Politely stop a process | `kill -15 [PID]` |
| Force stop a process | `kill -9 [PID]` |
| Restart a service safely | `sudo systemctl restart [service]` |
| Reload config (no downtime) | `sudo systemctl reload [service]` |

### Golden Rules for Basic Tuning

> 1. **Measure before you act.** Use `top`, `ps`, and `uptime` to understand the problem first.
> 2. **Isolate before you kill.** Use `renice +19` to reduce impact while you investigate.
> 3. **SIGTERM before SIGKILL.** Always give the process a chance to clean up.
> 4. **Use systemctl for services.** Never `kill -9` a service process — use `systemctl restart`.
> 5. **Fix the root cause.** Killing the process stops the bleeding — fixing the code prevents the next incident.
