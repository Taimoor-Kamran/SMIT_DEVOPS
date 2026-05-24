# Class 2: Branching, Trunk-Based Development & Feature Flags

---

## 1. What is a Branch?

A **branch** is an independent copy of your project where you can make changes safely — without touching the main working code.

Think of it like a parallel universe — you experiment in your universe, and only merge it into the real world when it is ready.

### Real-World Example

Imagine your team's website is live and working. You need to add a new "Contact Us" page.

Without branches, you would edit the live code directly — and if something breaks, the whole website goes down.

With branches, you create a separate `feature/contact-page` branch, build the page there, test it, and only merge it into the live code when it is perfect.

### How Branches Look in Git

```
main branch (always working, always live):
  A ── B ── C ──────────────── G   ← main stays safe
              \              /
               D ── E ── F         ← feature branch (your changes)
```

- `A B C` = commits already on main (live code)
- `D E F` = your new commits on the feature branch
- `G` = the merge commit — your work joins main

---

## 2. Git Branch Commands

### Create a Branch

```bash
# Create a new branch called feature/contact-page
git branch feature/contact-page
```

> This creates the branch but does NOT move you to it. You are still on `main`.

### Switch to a Branch

```bash
# Move to your new branch
git switch feature/contact-page
```

Output:

```
Switched to branch 'feature/contact-page'
```

```bash
# Shortcut — create AND switch in one command
git switch -c feature/contact-page
```

### List All Branches

```bash
# Show all branches — the one with * is where you currently are
git branch
```

Output:

```
* feature/contact-page    ← you are here
  main
```

### Delete a Branch (After Merging)

```bash
# Delete a branch you no longer need
git branch -d feature/contact-page
```

---

## 4. Trunk-Based Development

**Trunk-based development** is a workflow where every developer commits directly to `main` (the trunk) — or uses very short-lived branches that merge back within a day or two.

> The word **trunk** is just another name for the `main` branch — the single source of truth for your project.

### Traditional Branching vs Trunk-Based Development

| | Traditional Branching | Trunk-Based Development |
|---|---|---|
| Branch life | Days or weeks | Hours or 1–2 days max |
| Merge conflicts | Frequent and large | Rare and small |
| How often code reaches main | Rarely | Many times per day |
| Used by | Older teams | Google, Facebook, Netflix |

### Why Trunk-Based Development Works

The longer a branch lives, the harder it is to merge. When developers work on separate branches for weeks, the branches drift apart — and merging becomes painful.

Trunk-based development solves this by keeping everyone close to the same codebase at all times.

### What It Looks Like

```
Traditional (long branches — hard to merge):

  main:    A ──────────────────────────── merge (painful!)
                \                       /
  feature:       B ── C ── D ── E ── F     (2 weeks of work)

Trunk-Based (short branches — easy to merge):

  main:    A ── B ── C ── D ── E ── F ── G   (small, frequent commits)
                  \  /      \  /
                  br1        br2              (each branch lives < 1 day)
```

---

## 5. Feature Flags (Concept)

A **feature flag** (also called a feature toggle) is an `if/else` condition in your code that turns a feature ON or OFF — without deploying new code.

You merge the new feature into `main` while it is hidden behind the flag. When you are ready to release it, you flip the flag to ON — no deployment needed.

### Real-World Example

Your team has built a new checkout page. It is merged into `main` but not shown to users yet.

```python
# config.py — one file controls what is ON and OFF
FEATURES = {
    "new_checkout_page": False    # OFF — users see the old page
}
```

```python
# app.py — the flag controls which page loads
if FEATURES["new_checkout_page"]:
    show_new_checkout()
else:
    show_old_checkout()           # users see this while flag is OFF
```

When your team is ready to release:

```python
FEATURES = {
    "new_checkout_page": True     # flip to ON — all users now see the new page
}
```

> No new commit needed. No deployment needed. Just change one value in a config file.

### Why Feature Flags and Trunk-Based Development Go Together

| Problem | Solution |
|---------|---------|
| Feature is not ready but code must go to main | Hide it behind a flag set to `False` |
| Need to release instantly without a deploy | Flip the flag to `True` |
| Something breaks in production | Flip the flag back to `False` — instant rollback |
| Want to test with only 10% of users | Set flag per-user, not globally |

> `-d` only deletes if the branch has already been merged. Use `-D` (capital D) to force delete an unmerged branch.

---

## 3. Merging a Branch into Main

**Merging** means taking the commits from your feature branch and combining them into `main`.

### Step-by-Step: Merge a Feature Branch

```bash
# Step 1: Go back to main first — you always merge INTO the branch you are on
git switch main
```

```bash
# Step 2: Merge your feature branch into main
git merge feature/contact-page
```

Output:

```
Updating 9b1c2d3..a3f2c91
Fast-forward
 contact.html | 20 ++++++++++++++++++++
 1 file changed, 20 insertions(+)
 create mode 100644 contact.html
```

```bash
# Step 3: Delete the feature branch — it has been merged, no longer needed
git branch -d feature/contact-page
```

---

## 6. Lab

### Task 1 — Create a Feature Branch and Merge It

**Goal:** Practice the full branch lifecycle — create, commit, merge, delete.

**Step 1 — Create and switch to a new branch:**

```bash
git switch -c feature/about-page
```

**Step 2 — Create a file and commit it on the branch:**

```bash
echo "About page content" > about.html   # create a new file
git add about.html
git commit -m "Add about page"
```

**Step 3 — Switch back to main and merge:**

```bash
git switch main                          # go to main first
git merge feature/about-page             # bring in the feature branch work
git branch -d feature/about-page         # clean up the branch
```

**What to check:** Run `git log --oneline` — you should see the "Add about page" commit now on main.

---

### Task 2 — Simulate a Feature Toggle Config

**Goal:** See how a feature flag controls which code runs — without changing the app logic.

**Step 1 — Create a config file with the flag set to OFF:**

```python
# config.py
FEATURES = {
    "dark_mode": False    # OFF — users see the normal (light) theme
}
```

**Step 2 — Write the app logic that reads the flag:**

```python
# app.py
from config import FEATURES

if FEATURES["dark_mode"]:
    print("Loading dark theme")    # runs when flag is True
else:
    print("Loading light theme")   # runs when flag is False
```

**Step 3 — Run the script, then flip the flag and run again:**

```bash
python3 app.py        # Output: Loading light theme

# Now open config.py and change False to True, then run again:
python3 app.py        # Output: Loading dark theme
```

> You changed behaviour without touching app.py at all — that is the power of feature flags.

---

## 7. Incident — Wrong Branch, Fix with Rebase

### What Happened

A developer meant to work on `feature/login` but forgot to switch branches.

They made two commits directly on `main` by accident:

```
main: A ── B ── C ── D ── E    ← D and E were meant for feature/login
```

### What is Rebase?

**Rebase** moves your commits from one branch and replays them on top of another branch.

Think of it like cutting and pasting your commits to a different starting point.

```
Before rebase:
  main:          A ── B ── C
  feature/login:            D ── E    ← started from C, but we need it on main at C

After rebase (feature/login rebased onto latest main):
  main:          A ── B ── C ── D' ── E'   ← D and E replayed on top of C
```

> `D'` and `E'` have the same changes as `D` and `E` but they are new commits with new IDs.

### How to Fix It — Step by Step

**Step 1 — Find the commit where you accidentally started working on main:**

```bash
git log --oneline    # look for the last good commit before your accidental work
```

Output:

```
e7f1a2b Add login validation       ← accidental commit (was meant for feature branch)
c4d3b9a Add login form             ← accidental commit (was meant for feature branch)
9a2c1d0 Fix homepage layout        ← this is the last clean commit on main
```

**Step 2 — Create the correct feature branch from the last clean commit:**

```bash
# Create the feature branch pointing at the last clean commit (9a2c1d0)
git branch feature/login 9a2c1d0
```

**Step 3 — Move the accidental commits off main using rebase:**

```bash
git switch feature/login          # go to the new feature branch
git rebase main                   # replay the commits on top of current main
```

**Step 4 — Remove the accidental commits from main:**

```bash
git switch main                   # go back to main
git reset --hard 9a2c1d0          # move main back to the last clean commit
```

> `--hard` wipes those commits from main completely. The commits are safe — they are now on `feature/login`.

### Result After the Fix

```
main:          A ── B ── C                         ← clean, no accidental commits
feature/login:             D' ── E'                ← login work is now on the right branch
```

### Lesson Learned

Always run `git branch` before starting work — confirm you are on the right branch before your first commit.

---

## 8. Quick Reference

| Command | What it does |
|---------|-------------|
| `git branch feature/x` | Create branch (stay where you are) |
| `git switch feature/x` | Move to that branch |
| `git switch -c feature/x` | Create AND switch in one step |
| `git branch` | List all branches (shows which you are on) |
