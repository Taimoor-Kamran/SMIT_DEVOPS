# Class 5: Processes, Signals & Monitoring (ps, top, htop, kill)

---

## Setup — Start Some Processes First

Before we monitor anything, we need some processes running so there is something to observe.

```bash
# Start a few background processes
sleep 1000 &
sleep 2000 &
yes > /dev/null &    # CPU eater
yes > /dev/null &    # one more CPU eater
```

- `sleep` just waits silently — good for watching sleeping processes
- `yes > /dev/null` prints "y" forever and throws output away — it maxes out a CPU core
- `&` at the end sends the process to the background so your terminal stays free

---

## 1. ps — Process Snapshot

### Basic Commands

```bash
# See only your own session's processes
ps

# See ALL processes from ALL users — full detail
ps aux
```

Sample output of `ps aux`:

```
USER    PID  %CPU %MEM    VSZ   RSS  STAT  COMMAND
root      1   0.0  0.1  12345  1234  Ss    /sbin/init
student 1234 99.0  0.0   1234   456  R     yes
```

### Column Meanings

```
USER    → Who started the process
PID     → Process ID (unique number the OS assigns)
%CPU    → How much CPU it is consuming
%MEM    → How much RAM it is consuming
STAT    → Current state of the process (R/S/Z/T)
COMMAND → Which program is running
```

### STAT Column — Process States

```
R = Running       (currently using the CPU right now)
S = Sleeping      (waiting for something, doing nothing)
Z = Zombie        (finished but parent has not cleaned it up yet)
T = Stopped       (paused / frozen)
```

### Useful ps Commands

```bash
# Find a specific process by name
ps aux | grep yes

# Tree format showing parent-child relationships
ps axjf

# Extract just the PIDs (useful in scripts)
ps aux | grep yes | awk '{print $2}'
```

---

## 2. top — Live Process Monitor

```bash
top
```

### Screen Breakdown

```
top - 10:30:01 up 2:15, 2 users, load average: 1.98, 1.45, 0.89
Tasks: 120 total,   3 running, 117 sleeping
%Cpu(s): 98.2 us,  0.8 sy,  0.0 ni,  0.5 id
MiB Mem:   3900 total,   300 free,  2100 used
MiB Swap:  2048 total,  2000 free,    48 used
```

### Load Average Explained

```
Load Average: 1.98, 1.45, 0.89
              ↑     ↑     ↑
           1 min  5 min  15 min

On a 1-core system:
  1.0 = fully loaded (acceptable)
  2.0 = overloaded — processes are queuing up!

On a 4-core system:
  4.0 = fully loaded
  8.0 = overloaded
```

### CPU Breakdown Explained

```
%Cpu(s): 98.2 us,  0.8 sy,  0.0 ni,  0.5 id

  us = user processes (our programs eating CPU)
  sy = system/kernel work
  ni = nice (low priority processes)
  id = idle — the higher this is, the healthier the system
```

### Keyboard Shortcuts Inside top

```
q         → Quit
k         → Kill a process (it will ask for PID then signal)
r         → Renice — change priority of a process
M         → Sort by Memory usage
P         → Sort by CPU usage (default)
1         → Show each CPU core separately
h         → Help screen
```

### Live Demo Sequence

```bash
# 1. Open top
top

# 2. Press P → sorts by CPU — yes process jumps to the top
# 3. Press M → sorts by memory usage
# 4. Press 1 → see each CPU core individually
# 5. Press k → type the PID of a yes process → press Enter → type 9 → kill it
```

---

## 3. htop — Better Version of top

```bash
# If not installed:
sudo apt install htop

htop
```

### top vs htop Comparison

```
top                              htop
─────────────────────────────────────────────────────
Keyboard only                    Mouse also works
Text only                        Colors + visual bars
Kill one by one                  Select multiple then kill together
Fixed columns                    Columns are customizable
Available on every system        Needs to be installed
```

### htop Screen Layout

```
  1  [||||||||||||||||100%]    CPU bar — red means danger, fully loaded
  2  [|||||||          45%]
Mem  [|||||||||        60%]    Memory bar
Swp  [|                 2%]    Swap bar — should stay near 0

  PID  USER     NI  VIRT  RES  CPU%  MEM%  COMMAND
 1234  student   0  1234  456  99.0   0.1  yes
```

### htop Keyboard Shortcuts

```
F3 or /    → Search process by name
F5         → Tree view — shows parent and child processes
F6         → Choose which column to sort by
F9         → Kill menu — choose which signal to send
F10        → Quit
Space      → Select a process (can select multiple)
u          → Filter by username
```

### Live Demo

```bash
htop

# 1. Press "/" → type "yes" → press Enter
# 2. The yes processes get highlighted
# 3. Press F9 → select SIGKILL → press Enter
# 4. yes process disappears from the list
```

---

## 4. Signals & kill

### What is a Signal?

A signal is a message the OS sends to a process to give it an instruction.

```
Process is running → We send a signal → Process reacts to it
```

### Most Important Signals

```
Signal      Number    Meaning
─────────────────────────────────────────────────────────
SIGTERM       15      "Please stop" — polite request
SIGKILL        9      "DIE NOW — no mercy, no cleanup"  ← force
SIGSTOP       19      "Pause / freeze"
SIGCONT       18      "Resume / continue"
SIGHUP         1      "Reload your config file"
```

### kill Commands — Practical Usage

```bash
# First find the PID
ps aux | grep yes

# Send SIGTERM — polite stop (process cleans up then exits)
kill 1234
kill -15 1234        # same thing
kill -SIGTERM 1234   # same thing

# If the process ignores SIGTERM — force kill with SIGKILL
kill -9 1234
kill -SIGKILL 1234

# Kill all processes matching a name at once
pkill yes

# Kill by exact name
killall yes
```

### SIGTERM vs SIGKILL — The Key Difference

```
SIGTERM (-15):                      SIGKILL (-9):
──────────────────                  ─────────────────
OS asks the process to stop         OS kills the process directly
                                    without asking

The process CAN:                    The process gets:
✓ Close open files properly         ✗ No chance to do anything
✓ Save data before exiting          ✗ Data may be lost or corrupted
✓ Release locks and connections     ✗ Files may be left in bad state

The process CAN ignore it!          Cannot be ignored — ever

Use for: normal shutdown            Use for: frozen / stuck process
```

### SIGSTOP / SIGCONT — Pause and Resume

```bash
# Pause a process — it stops using CPU but stays alive
kill -19 1234
kill -SIGSTOP 1234

# Check top now → STAT column shows "T" (stopped)

# Resume it
kill -18 1234
kill -SIGCONT 1234
```

---

## Full Live Demo — Step by Step

### Step 1: Create the CPU Load

```bash
yes > /dev/null &
yes > /dev/null &
echo "Last background PID: $!"
```

### Step 2: Check with ps

```bash
ps aux | grep yes
```

### Step 3: Watch in top

```bash
top
# Press P → yes processes appear at the top
# Watch %CPU → near 100% per core
```

### Step 4: Watch in htop

```bash
htop
# CPU bars turn red — fully loaded
# Press / → type "yes" → search
```

### Step 5: Try All the Signals

```bash
# Note the PIDs first
ps aux | grep yes

# Pause one — watch CPU drop in top
kill -SIGSTOP [PID1]
# In top: STAT column shows "T", CPU usage drops

# Resume it
kill -SIGCONT [PID1]
# CPU usage jumps back up

# Politely terminate one
kill -15 [PID1]

# Force kill the other
kill -9 [PID2]

# Clean up everything remaining
pkill yes
```

