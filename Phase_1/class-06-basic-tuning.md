# Class 6: Basic Tuning

---

## 1. What Is Basic Tuning?

**Tuning** means making your Linux system run more efficiently — without changing the hardware or rewriting any code.

As a sysadmin or DevOps engineer, basic tuning means:

- Spotting which processes are using too much CPU or memory
- Adjusting their priority so important work gets done first
- Knowing when and how to safely stop or restart a misbehaving process

### Why It Matters

| Without Tuning | With Tuning |
|----------------|-------------|
| One runaway process can freeze everything | Resources are shared fairly |
| You only find out about problems after a crash | Problems are caught before users are affected |
| No idea what is eating your CPU | Clear picture of every running process |
| Services are killed incorrectly and lose data | Processes are stopped cleanly and safely |

---

## 2. Key Metrics — What to Look at First

Before you change anything, you need to understand what the system is telling you.

### Load Average

```bash
uptime   # shows how long the system has been running and the load average
```

Output:

```
10:45:01 up 3:20, 1 user, load average: 2.45, 1.87, 1.12
                                         ↑     ↑     ↑
                                       1 min  5 min  15 min
```

Load average = how many processes are waiting to use the CPU at a given moment.

```
Rule: if load average > number of CPU cores, the system is stressed

Check your core count:
  nproc

1-core system:  load 1.0 = fully loaded,  load 2.0 = overloaded
4-core system:  load 4.0 = fully loaded,  load 8.0 = overloaded
```

### CPU Usage Breakdown

```bash
top   # opens a live dashboard — updates every few seconds
```

Header line inside top:

```
%Cpu(s): 89.3 us,  3.2 sy,  0.0 ni,  7.0 id,  0.5 wa
          ↑         ↑         ↑         ↑         ↑
        user      system    nice      idle      I/O wait
```

| Field | Meaning | What to Watch For |
|-------|---------|-------------------|
| `us` | CPU used by your programs | Depends on workload |
| `sy` | CPU used by the Linux kernel | Should stay below 5% |
| `ni` | CPU used by low-priority processes | Varies |
| `id` | Idle — CPU doing nothing | Higher means healthier |
| `wa` | Waiting for disk or network I/O | Should stay below 10% |

> **Key rule:** if `id` (idle) is close to 0%, your CPU is maxed out. If `wa` is high, the bottleneck is disk or network — not CPU.

### Memory Usage

```bash
free -h   # shows RAM and swap in human-readable sizes (K, M, G)
```

Output:

```
              total   used    free    shared  buff/cache  available
Mem:          3.8G    2.1G    300M    100M    1.4G        1.5G
Swap:         2.0G    50M     1.9G
```

| Field | Meaning |
|-------|---------|
| `total` | Total RAM installed on the machine |
| `used` | RAM currently allocated to processes |
| `available` | What new programs can actually use right now |
| `Swap used` | If this keeps growing, RAM is full — serious warning sign |
