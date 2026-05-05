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

