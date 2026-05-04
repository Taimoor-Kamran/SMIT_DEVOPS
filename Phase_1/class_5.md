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

---

## 4. htop — Improved Interactive Process Viewer

`htop` is an enhanced version of `top` with a visual interface, colors, and mouse support.  
It is not installed by default — install it first:

```bash
sudo apt install htop -y    # Ubuntu/Debian
sudo yum install htop -y    # CentOS/RHEL
```

Run it:

```bash
htop
```

### What htop Shows Better Than top

| Feature | top | htop |
|---------|-----|------|
| CPU bars per core | Text only | Color bars |
| Memory bar | Text only | Color bar |
| Mouse support | No | Yes |
| Scroll processes | No | Yes |
| Tree view | No | Yes (F5) |
| Kill process | Enter PID manually | Select + F9 |
| Search process | No | F3 |

### htop Keyboard Shortcuts

| Key | Action |
|-----|--------|
| `F2` | Setup / configuration |
| `F3` | Search for a process by name |
| `F4` | Filter processes |
| `F5` | Toggle tree view (shows parent-child) |
| `F6` | Sort by column |
| `F9` | Send signal / kill selected process |
| `F10` | Quit |
| `Space` | Tag/select a process |
| `u` | Filter by user |

### htop vs top — When to Use Which

| Use `top` when | Use `htop` when |
|---------------|----------------|
| Server has no extra packages | htop is installed |
| Need scriptable output | Need interactive visual view |
| Quick one-time check | Diagnosing CPU/memory issues |

---

## 5. Signals — Communicating with Processes

A **signal** is a message sent to a process to tell it to do something.  
Signals are the way Linux controls running processes — pause, stop, restart, terminate.

### Most Important Signals

| Signal | Number | Name | Meaning |
|--------|--------|------|---------|
| SIGTERM | 15 | Terminate | Politely ask process to stop (can be caught) |
| SIGKILL | 9 | Kill | Force-kill immediately (cannot be ignored) |
| SIGHUP | 1 | Hangup | Reload config without restarting |
| SIGSTOP | 19 | Stop | Pause the process |
| SIGCONT | 18 | Continue | Resume a paused process |
| SIGINT | 2 | Interrupt | Same as pressing Ctrl+C |

### Rule: Always Try SIGTERM Before SIGKILL

```
SIGTERM → gives process time to clean up (close files, flush logs)
SIGKILL → instant death, no cleanup, can leave corrupted data
```

---

## 6. kill, killall, pkill — Sending Signals to Processes

### kill — Send Signal by PID

```bash
kill PID               # Default: sends SIGTERM (15) — graceful stop
kill -15 PID           # Explicit SIGTERM
kill -9 PID            # SIGKILL — force kill (last resort)
kill -1 PID            # SIGHUP — reload config
kill -SIGTERM PID      # Same as kill -15 (name form)
```

**Step-by-step example:**

```bash
# Step 1: Find the PID of nginx
ps aux | grep nginx

# Output:
# nginx   1234  0.0  0.1  12000 1400 ?  S  10:00  nginx: master

# Step 2: Try graceful stop first
kill -15 1234

# Step 3: If it doesn't stop, force kill
kill -9 1234
```

### killall — Kill by Process Name

```bash
killall nginx           # Send SIGTERM to all nginx processes
killall -9 python       # Force kill all python processes
killall -HUP nginx      # Reload nginx config (SIGHUP)
```

### pkill — Kill by Pattern Match

```bash
pkill nginx             # Kill processes matching name "nginx"
pkill -u taimoor        # Kill all processes owned by taimoor
pkill -9 myapp          # Force kill processes matching "myapp"
pkill -f "python app"   # Match against full command line
```

### View All Available Signal Numbers

```bash
kill -l
```

Output:

```
 1) SIGHUP   2) SIGINT   3) SIGQUIT  9) SIGKILL  15) SIGTERM
19) SIGSTOP 18) SIGCONT ...
```

