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

