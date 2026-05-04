# Class 5: Processes, Signals & Resource Monitoring

---

## 1. What is a Process?

A **process** is any running program on Linux.  
When you run a command like `nginx` or `python app.py`, the OS creates a process for it.  
Every process gets a unique **PID (Process ID)** assigned by the kernel.

### Process States

| State | Symbol | Meaning |
|-------|--------|---------|
| Running | `R` | Actively using CPU |
| Sleeping | `S` | Waiting for input or event |
| Uninterruptible sleep | `D` | Waiting on disk/network I/O |
| Stopped | `T` | Paused (e.g. by Ctrl+Z) |
| Zombie | `Z` | Finished but not yet cleaned up by parent |

### Process Hierarchy

Every process on Linux has a parent.  
The first process is **`systemd`** (PID 1) — it starts everything else.

```
PID 1 (systemd)
  └── sshd
       └── bash
            └── python app.py   ← your process
```

Check PID 1:

```bash
ps -p 1
```

---

## 2. ps — Snapshot of Running Processes

`ps` prints a **snapshot** of processes at the moment you run it.  
It does not update automatically — it is a one-time report.

### Common ps Commands

```bash
ps                        # Show only your current session's processes
ps aux                    # Show ALL processes from ALL users (most used)
ps -ef                    # Full format listing — same coverage as aux
ps -u taimoor             # Processes owned by a specific user
ps -p 1234                # Show process with specific PID
ps aux --sort=-%cpu       # Sort by CPU usage (highest first)
ps aux --sort=-%mem       # Sort by memory usage (highest first)
```

### Understanding ps aux Output

```bash
ps aux
```

Output:

```
USER       PID  %CPU  %MEM    VSZ   RSS  TTY  STAT  START   TIME  COMMAND
root         1   0.0   0.1  16952  1200  ?    Ss    10:00   0:01  /sbin/init
taimoor   1234   2.5   1.4  45000  5600  pts/0 S    10:05   0:10  python app.py
nginx     5678   0.1   0.3  12000  1400  ?    S     10:01   0:00  nginx: worker
```

Column breakdown:

| Column | Meaning |
|--------|---------|
| `USER` | Who owns the process |
| `PID` | Process ID |
| `%CPU` | CPU usage percentage |
| `%MEM` | Memory usage percentage |
| `VSZ` | Virtual memory size (KB) |
| `RSS` | Real memory in use (KB) |
| `STAT` | Process state (S=sleep, R=running, Z=zombie) |
| `COMMAND` | The command that started it |

### Find a Specific Process

```bash
ps aux | grep nginx          # Find nginx processes
ps aux | grep python         # Find python processes
ps aux | grep -v grep        # Exclude the grep itself from results
```

---

## 3. top — Live Process Monitor

`top` shows a **live, auto-refreshing** view of all processes and system resources.  
It updates every 3 seconds by default.

```bash
top
```

### Reading the top Header

```
top - 10:05:01 up 2 days, 3:10,  2 users,  load average: 0.25, 0.18, 0.12
Tasks: 120 total,   1 running, 119 sleeping,   0 stopped,   0 zombie
%Cpu(s):  5.2 us,  1.0 sy,  0.0 ni, 93.5 id,  0.1 wa,  0.0 hi,  0.2 si
MiB Mem :   7850.0 total,   2100.0 free,   3200.0 used,   2550.0 buff/cache
MiB Swap:   2048.0 total,   2048.0 free,      0.0 used.   4200.0 avail Mem
```

| Field | Meaning |
|-------|---------|
| `up 2 days` | System uptime |
| `load average: 0.25` | Avg processes waiting for CPU (1m, 5m, 15m) |
| `%Cpu us` | User space CPU usage |
| `%Cpu id` | Idle CPU (higher = less busy) |
| `wa` | Waiting on disk I/O (high = storage bottleneck) |
| `Mem used` | RAM currently in use |
| `buff/cache` | Memory used for disk caching (can be freed) |

### Keyboard Shortcuts Inside top

| Key | Action |
|-----|--------|
| `P` | Sort by CPU usage |
| `M` | Sort by memory usage |
| `k` | Kill a process (enter PID) |
| `u` | Filter by username |
| `1` | Show each CPU core individually |
| `q` | Quit |
| `h` | Help |

### Run top Non-Interactively

```bash
top -b -n 1              # Run once, print output and exit (good for scripts)
top -b -n 1 | head -20   # Show top 20 lines of output
top -u taimoor           # Show only processes for a specific user
```

