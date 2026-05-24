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

> `-d` only deletes if the branch has already been merged. Use `-D` (capital D) to force delete an unmerged branch.

---

## 3. Merging a Branch into Main

**Merging** means taking the commits from your feature branch and combining them into `main`.
