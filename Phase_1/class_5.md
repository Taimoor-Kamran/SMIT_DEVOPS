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

