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

---

## 3. Identifying Heavy Processes

Once you know the system is stressed, you need to find *what* is causing it.

### Sort by CPU Usage

```bash
# Show all processes — heaviest CPU consumer at the top
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

### Sort by Memory Usage

```bash
# Show all processes — biggest memory consumer at the top
ps aux --sort=-%mem | head -15
```

### Use top for a Live View

```bash
top   # opens a live dashboard — updates automatically every few seconds
```

Useful keys inside top:

```
P   → sort by CPU (the worst offender jumps to the top)
M   → sort by memory usage
k   → kill a process (asks for the PID, then the signal)
q   → quit top
```

---

## 4. Simulating Load — Creating a Test Environment

Before you can practise identifying problems, you need to create some. The simplest way is with `yes`.

```bash
# yes prints "y" forever — piped to /dev/null so output is thrown away
# The result: one process eating 100% of a CPU core
yes > /dev/null &

# Start three to saturate multiple cores at once
yes > /dev/null &
yes > /dev/null &
yes > /dev/null &
```

The `&` at the end sends the process to the background so your terminal stays free.

```bash
# Confirm the load jumped up
uptime

# See the yes processes at the top, eating nearly 100% each
ps aux --sort=-%cpu | head -10

# Clean up everything when you are done
pkill yes
```

---

## 5. Nice Values — Adjusting Process Priority

### What Is a Nice Value?

Every process has a **nice value** that tells the OS how much CPU priority to give it.

```
Range:   -20  ←────────────── 0 ──────────────→  +19
          ↑                   ↑                    ↑
      greediest            default            most polite
   (gets CPU first)     (normal apps)    (gives way to others)
```

Think of it like a queue — a lower nice value means you get served before everyone else.

### When to Use Nice Values

| Situation | Nice Value | Effect |
|-----------|-----------|--------|
| Web server, database | -5 to 0 | Gets CPU before other processes |
| Normal user apps | 0 | Default — fair share |
| Background backup job | +10 | Gives way to important work |
| Low-priority batch job | +19 | Only runs when CPU is completely idle |

### Start a New Process with a Set Priority

```bash
# Start yes at low priority (nice = 10 — more polite than default)
nice -n 10 yes > /dev/null &

# Start yes at the lowest possible priority
nice -n 19 yes > /dev/null &
```

### Change the Priority of an Already Running Process

```bash
# Find the PID of the process you want to adjust
ps aux | grep yes

# Make it more polite — give way to other processes (higher number = lower priority)
sudo renice +15 -p 3421

# Make it greedier — get more CPU time (lower number = higher priority)
sudo renice -5 -p 3421
```

> **Note:** Only root can set negative nice values (below 0). Any regular user can make their own processes nicer by raising the nice value.

### Verify the Change Worked

```bash
# The NI column shows the current nice value
ps aux | grep yes

# Or open top and look at the NI column next to each process
top
```
