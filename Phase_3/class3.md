# Class 3: Advanced Bash — Loops, Conditionals and Cron

---

## Table of Contents
1. [Conditionals](#conditionals)
2. [Loops](#loops)
3. [Cron — Scheduled Tasks](#cron--scheduled-tasks)
4. [Lab: Monitoring Script with Alerts](#lab-monitoring-script-with-alerts)
5. [Incident: Logic Bug Causes False Alerts](#incident-logic-bug-causes-false-alerts)
6. [Quick Reference](#quick-reference)

---

## Conditionals

A **conditional** lets your script make a decision — run one block of code if something is true, and a different block if it is false.
Think of it like a fork in the road: your script chooses which path to take based on a condition.

### if / elif / else

```bash
if [ condition ]; then
    # runs when condition is TRUE
elif [ other_condition ]; then
    # runs when other_condition is TRUE
else
    # runs when ALL conditions are FALSE
fi                          # "fi" closes the if block (if spelled backwards)
```

**Beginner Example — check a number:**
```bash
#!/bin/bash
score=75

if [ $score -ge 90 ]; then
    echo "Grade: A"
elif [ $score -ge 75 ]; then
    echo "Grade: B"
elif [ $score -ge 60 ]; then
    echo "Grade: C"
else
    echo "Grade: F — Please study harder!"
fi
```

Output: `Grade: B`
