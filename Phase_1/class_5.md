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

