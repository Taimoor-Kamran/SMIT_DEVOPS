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

**What does load average mean?**
It shows how busy your system has been over the last 1 minute, 5 minutes, and 15 minutes.
Think of it like a queue at a shop — the number tells you how many customers are waiting.

**How to interpret it:**

```
Number of CPU cores = how much load is "normal"

1-core system:
  load 1.0  → fully loaded (still okay)
  load 2.0  → overloaded — processes are waiting for CPU

4-core system:
  load 4.0  → fully loaded (still okay)
  load 8.0  → overloaded — serious problem

Rule: if load average > number of CPU cores, the system is stressed
```

Check how many CPU cores your machine has:

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

When you run top, look at the header line at the top:

```
%Cpu(s): 89.3 us,  3.2 sy,  0.0 ni,  7.0 id,  0.5 wa
          ↑         ↑         ↑         ↑         ↑
        user      system    nice      idle      I/O wait
```

| Field | What it means | Healthy value |
|-------|--------------|---------------|
| `us` | CPU used by your programs | Depends on workload |
| `sy` | CPU used by Linux itself (the kernel) | Should be less than 5% |
| `ni` | CPU used by low-priority programs | Varies |
| `id` | Idle — CPU is doing nothing | Higher is better |
| `wa` | CPU waiting for disk or network | Should be less than 10% |

> **Golden rule:**
> - If `id` (idle) is near 0% → your CPU is completely maxed out
> - If `wa` is high → the problem is your disk or network, not CPU

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

| Field | What it means |
|-------|--------------|
| `total` | Total RAM installed in your machine |
| `used` | RAM currently being used |
| `available` | RAM that new programs can actually use |
| `Swap used` | If this keeps growing, your RAM is full — serious problem |

> **What is Swap?**
> When your RAM is full, Linux uses a part of your hard disk as temporary memory.
> This is called Swap. It is much slower than RAM, so growing Swap = bad sign.

---

## 3. Identifying Heavy Processes

A process is any running program. Sometimes one process eats too much CPU or memory and slows everything down. Here is how to find it.

### Method 1 — Find the Top CPU Users

```bash
# Show the processes using the most CPU — heaviest first
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

> **What is PID?**
> PID = Process ID. Every running program gets a unique number.
> You use this number to control the process (change its priority, stop it, etc.)

### Method 2 — Find the Top Memory Users

```bash
# Show the processes using the most memory — heaviest first
ps aux --sort=-%mem | head -15
```

### Method 3 — Watch Processes Live with top

```bash
top
```

Once top is open, you can use these keys:

```
P  → Sort by CPU usage    (heaviest process jumps to top)
M  → Sort by Memory usage
k  → Kill a process       (it will ask for PID, then signal number)
q  → Quit top
```

---

## 4. Simulating Load (For Testing and Learning)

Before you can practice fixing heavy processes, you need to create some.
We use a command called `yes` to do this safely.

> **What does `yes > /dev/null &` mean?**
>
> | Part | What it does |
> |------|-------------|
> | `yes` | A program that prints the letter "y" forever, as fast as possible |
> | `>` | Sends the output somewhere (instead of showing it on screen) |
> | `/dev/null` | A trash bin — anything sent here is thrown away immediately |
> | `&` | Runs the command in the background so your terminal stays usable |
>
> Together: this command burns 100% of one CPU core without printing anything.

```bash
# Burn 100% of one CPU core
yes > /dev/null &

# Burn multiple cores — run one command per core you want to saturate
yes > /dev/null &
yes > /dev/null &
yes > /dev/null &
yes > /dev/null &
```

---

## 5. Nice Values — Process Priority

### What is a Nice Value?

Every process in Linux has a **nice value**. This tells the OS how much CPU time to give it compared to other processes.

```
Nice Value Range: -20 to +19

  -20  →  Highest priority (gets CPU first — very greedy)
    0  →  Default priority (what every new process starts at)
  +19  →  Lowest priority (gives way to everything else — very polite)
```

> **Easy way to remember:**
> Think of it like a queue at a shop.
> - Nice value **-20** = you push to the front of the line
> - Nice value **0** = you wait your turn like everyone else
> - Nice value **+19** = you let everyone else go first

### Why Nice Values Matter

If a background job is consuming CPU and slowing down your web server:

1. Increase the background job's nice value → it becomes "polite" and gives way
2. The web server gets CPU time first → it runs smoothly again
3. The background job still finishes — just a bit slower

### Start a New Process with a Custom Nice Value

```bash
# Start a process with low priority (nice = 10)
nice -n 10 yes > /dev/null &

# Start a process with the lowest possible priority (nice = 19)
nice -n 19 yes > /dev/null &
```

### Change the Priority of a Process Already Running (renice)

```bash
# Step 1: Find the PID of the process
ps aux | grep yes

# Step 2: Lower its priority (make it give way to others)
sudo renice +15 -p 3421

# Step 3: Raise its priority (make it get more CPU)
sudo renice -5 -p 3421
```

> **Important rules:**
> - Only root (admin) can set a negative nice value (below 0)
> - Any user can make their own process nicer (increase the nice value)

### Verify the Nice Value Changed

```bash
# Check the NI column in the output
ps aux | grep yes

# Or watch it live in top — look at the NI column
top
```

### Nice Value Comparison Table

| Situation | Nice Value | Effect |
|-----------|-----------|--------|
| Web server, database | -5 to 0 | Gets CPU before other processes |
| Normal user apps | 0 | Default — fair share of CPU |
| Background backup job | +10 | Yields CPU to more important work |
| Low-priority batch job | +19 | Only runs when CPU is completely idle |

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

> **What are SIGTERM and SIGKILL?**
> Linux communicates with processes using signals — these are like messages you send to a running program.
>
> | Signal | Number | What it does |
> |--------|--------|-------------|
> | SIGTERM | 15 | Politely asks the process to stop. The process can save files and clean up first. |
> | SIGKILL | 9 | Forces the OS to immediately destroy the process. No saving, no cleanup. |

**Why this order matters:**

```
SIGTERM allows the process to:        SIGKILL forces the OS to:
✓ Save open files and data            ✗ No saving — data can be lost
✓ Close database connections          ✗ Connections left hanging
✓ Write final log entries             ✗ Logs may be incomplete
✓ Release file locks                  ✗ Locks remain — next start may fail
```

> **Rule:** Always try SIGTERM first. Only use SIGKILL if SIGTERM does not work.

### Terminate a Process Safely — Step by Step

```bash
# Step 1: Find the PID of the process you want to stop
ps aux | grep bad_process

# Step 2: Send a polite stop request
kill -15 <PID>

# Step 3: Wait 5 seconds to give it time to shut down
sleep 5

# Step 4: Check if it is gone
ps aux | grep <PID>

# Step 5: If it is still running, force kill it
kill -9 <PID>
```

> Replace `<PID>` with the actual process ID number you found in Step 1.

### Terminate by Name (Easier)

```bash
# Politely stop all processes named "bad_process"
pkill bad_process

# Force kill all processes named "bad_process"
pkill -9 bad_process

# Alternative using killall
killall bad_process
killall -9 bad_process
```

### Restart a Service Safely (systemd)

> **What is systemd?**
> systemd is the system that manages services (like nginx, mysql) on Linux.
> Always use `systemctl` to restart services — never kill them directly.

```bash
# Safe restart — systemd sends SIGTERM, waits, then starts fresh
sudo systemctl restart nginx

# Stop only
sudo systemctl stop nginx

# Start only
sudo systemctl start nginx

# Check if it came back up correctly
sudo systemctl status nginx
```

### Reload Config Without a Full Restart

Some services can reload their settings without fully stopping. This means zero downtime.

```bash
# Reload nginx config — no dropped connections
sudo systemctl reload nginx
```

You can also send a signal called SIGHUP directly to a process:

```bash
# SIGHUP — what is it?
# HUP stands for "hang up" (an old telephone term).
# Most programs treat SIGHUP as a command to reload their config file.
# It does NOT stop the process — it just tells it to re-read its settings.
# Example: nginx gets SIGHUP and reloads nginx.conf without dropping any connections.
kill -1 $PID
kill -SIGHUP $PID
```

---

## 7. Lab: Hands-On Practice

### Task 1: Simulate Load and Watch the System React

```bash
# Step 1: Check your baseline — write down the current load average
uptime

# Step 2: Create 3 CPU-eating processes
yes > /dev/null &
yes > /dev/null &
yes > /dev/null &

# Step 3: Check load average again — it should be higher now
uptime

# Step 4: Watch what is happening in real time
top
# Press P to sort by CPU — you will see the yes processes at the top

# Step 5: Check with ps
ps aux --sort=-%cpu | head -10
```

### Task 2: Adjust Process Priority with renice

```bash
# Start two yes processes and save their PIDs into variables
yes > /dev/null &
PID_A=$!
echo "PID_A is: $PID_A"

yes > /dev/null &
PID_B=$!
echo "PID_B is: $PID_B"

# Give PID_A high priority (it gets more CPU)
sudo renice -10 -p $PID_A

# Give PID_B low priority (it gives way to others)
sudo renice +15 -p $PID_B

# Watch the difference in top
top
# Press P — you will see PID_A gets more CPU time than PID_B

# Verify the nice values changed
ps -o pid,ni,comm -p $PID_A $PID_B
```

### Task 3: Terminate Processes Safely

```bash
# First see all yes processes running
ps aux | grep yes

# Step 1: Politely stop PID_A
kill -15 $PID_A

# Wait 2 seconds and check if it is gone
sleep 2
ps aux | grep $PID_A
# Should show nothing (or only the grep command itself)

# Step 2: Force kill PID_B (simulating a stuck process)
kill -9 $PID_B

# Clean up any remaining yes processes
pkill yes

# Confirm none are left
ps aux | grep yes
```

### Task 4: Safe Service Restart

```bash
# Install nginx if not already installed
sudo apt install nginx

# Start nginx
sudo systemctl start nginx

# Check it is running
sudo systemctl status nginx

# Reload config without dropping connections
sudo systemctl reload nginx

# Do a full restart
sudo systemctl restart nginx

# Confirm it is active
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

Your job: find the cause, isolate it, and restore the system.

### Step 1: Get Immediate Situational Awareness

```bash
uptime
```

Output:

```
02:15:33 up 14 days, 2:10, 1 user, load average: 7.82, 6.91, 5.43
```

Load is nearly double the 4-core limit. Something is seriously wrong.

```bash
top
```

Top header:

```
%Cpu(s): 98.7 us,  0.8 sy,  0.0 ni,  0.1 id
```

`id` is 0.1% — the CPU is completely saturated.

### Step 2: Identify the Runaway Process

```bash
# Sort by CPU — find the biggest consumer immediately
ps aux --sort=-%cpu | head -10
```

Output:

```
USER      PID   %CPU %MEM  COMMAND
deploy   8821  197.2  1.2  python3 data_processor.py
www-data  412    1.1  0.8  nginx: worker process
mysql    1023    0.9  3.4  mysqld
```

Found it: `data_processor.py` is consuming 197% CPU (using 2 full cores).

> **Why does it show 197%?**
> On a multi-core system, one process can use more than 100% CPU — it just means it is using more than one core.

### Step 3: Gather Information Before Killing

Do not kill it yet. First understand what it is and why it is misbehaving.

```bash
# How long has it been running?
ps -p 8821 -o pid,etime,cmd
```

Output:

```
  PID     ELAPSED  CMD
 8821    02:47:13  python3 data_processor.py
```

Running for nearly 3 hours — far longer than expected.

```bash
# /proc is a special Linux folder that shows live information about every running process.
# /proc/8821 is the folder for process number 8821.
# /proc/8821/fd lists all the files that this process currently has open.
ls -la /proc/8821/fd | head -20

# Easier to read alternative:
lsof -p 8821

# Is this a scheduled job? Check the crontab for the deploy user
sudo crontab -u deploy -l
```

Output:

```
0 23 * * * /opt/scripts/data_processor.py
```

It is a nightly job that started at 23:00 but never finished. It is stuck in a loop.

```bash
# Check system logs for errors from this process
sudo journalctl --since "2 hours ago" | grep data_processor
```

Output:

```
02:00:01 web-server-01 python3[8821]: WARNING: retry attempt 1847 — connection timeout
02:00:04 web-server-01 python3[8821]: WARNING: retry attempt 1848 — connection timeout
```

The process is stuck retrying forever because the database it connects to went offline.

### Step 4: Isolate — Lower Its Priority First

Before killing it, lower its priority. This reduces the damage while you confirm your plan.

```bash
# Drop it to the lowest priority so nginx can breathe
sudo renice +19 -p 8821

# Verify it helped
top
```

Top header now:

```
%Cpu(s): 61.4 us,  2.1 sy,  2.3 ni, 34.1 id
```

Idle jumped from 0.1% to 34.1%. The web server is responding again.

### Step 5: Terminate the Runaway Process Safely

```bash
# Step 1: Try SIGTERM first — polite request to stop
sudo kill -15 8821

# Step 2: Wait and check
sleep 5
ps aux | grep 8821
```

Output:

```
deploy   8821  98.2  1.2  python3 data_processor.py
```

Still running — it is ignoring SIGTERM because it is stuck in a loop.

```bash
# Step 3: Force kill with SIGKILL
sudo kill -9 8821

# Step 4: Confirm it is gone
ps aux | grep 8821
# No output = process is gone
```

### Step 6: Verify System Recovery

```bash
uptime
```

Output (2 minutes later):

```
02:20:01 up 14 days, 2:15, 1 user, load average: 1.12, 3.45, 4.87
```

Load is falling. The 1-minute average is already healthy.

```bash
# Confirm nginx is still running
sudo systemctl status nginx
curl -I http://localhost
```

Output:

```
HTTP/1.1 200 OK
```

Web server is responding normally.

### Step 7: Root Cause and Fix

**Root cause:** `data_processor.py` had no maximum retry limit. When the database went offline, the script kept retrying forever.

**Immediate fix:** killed the runaway process (already done)

**Proper fix — add a retry limit to the code:**

```python
# Before fix — retries forever, no limit
while True:
    try:
        connect_to_database()
        break
    except ConnectionError:
        time.sleep(10)
        continue   # loops forever if database stays offline

# After fix — maximum 5 retries, then gives up
MAX_RETRIES = 5
for attempt in range(MAX_RETRIES):
    try:
        connect_to_database()
        break
    except ConnectionError:
        if attempt == MAX_RETRIES - 1:
            raise   # fail loudly after max retries so someone gets alerted
        time.sleep(10 * (2 ** attempt))   # wait longer each time (exponential backoff)
```

Restart the job manually once the database is back online:

```bash
# nc = netcat — a tool to test if a network port is reachable
# -z = just check if the port is open, do not send any data
# -v = show the result clearly (verbose)
# 5432 = the default port number for PostgreSQL (our database)
nc -zv db-server 5432
# If you see "succeeded" — the database is reachable

# Now re-run the job safely
sudo -u deploy python3 /opt/scripts/data_processor.py
```

### Incident Summary

| Step | What we did | Command used |
|------|------------|--------------|
| 1 | Checked load average | `uptime` |
| 2 | Found CPU at 98%, idle near zero | `top` |
| 3 | Identified the runaway process | `ps aux --sort=-%cpu` |
| 4 | Checked how long it had been running | `ps -p 8821 -o pid,etime,cmd` |
| 5 | Found root cause in the logs | `journalctl` |
| 6 | Reduced impact with renice | `sudo renice +19 -p 8821` |
| 7 | Tried polite stop first | `sudo kill -15 8821` |
| 8 | Force killed as last resort | `sudo kill -9 8821` |
| 9 | Verified system recovered | `uptime`, `curl` |
| 10 | Fixed the code | Added retry limit |

### Common Runaway Process Scenarios

| Symptom | Likely cause | What to do first |
|---------|-------------|-----------------|
| CPU 100%, `id` near 0 | Infinite loop or stuck process | `ps aux --sort=-%cpu` then renice and kill |
| Load average much higher than core count | Too many processes piled up | Find and kill the heaviest ones |
| Memory keeps growing over time | Memory leak in a long-running process | `ps aux --sort=-%mem \| head -10` |
| High `wa` in top | Process doing too much disk read/write | Check `wa` in top — if high, disk is the bottleneck |
| Service suddenly becomes slow | Another process stealing CPU | `renice` the offender to +19 |

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
| Count CPU cores | `nproc` |
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
> 5. **Fix the root cause.** Killing the process stops the bleeding — fixing the root cause stops it from happening again.
