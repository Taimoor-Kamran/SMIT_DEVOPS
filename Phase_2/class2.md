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
