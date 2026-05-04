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

