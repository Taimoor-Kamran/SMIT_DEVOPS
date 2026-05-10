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
