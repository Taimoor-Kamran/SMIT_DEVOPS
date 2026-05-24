# Class 1: Git Fundamentals

---

## 1. What is Git?

**Git** is a tool that tracks changes to your files over time.

Think of it like a save system in a video game — but instead of saving just the current state, Git remembers every save you ever made. You can go back to any point in history.

### Why Every Developer and DevOps Engineer Uses Git

| Without Git | With Git |
|-------------|---------|
| You accidentally delete a file — it is gone forever | You can restore any file from any point in history |
| You make a change that breaks everything | You can undo it instantly |
| Two people edit the same file — one person's work is lost | Git merges both sets of changes |
| No record of who changed what or why | Every change has a name, time, and message |

> **Key idea:** Git does not just save your files — it saves the *history* of your files.
> Each saved snapshot is called a **commit**.

---

## 2. Local vs Remote

Git works in two places at the same time.

### Local Repository

This is the copy of your project that lives **on your own computer**.
You do all your work here — writing code, making commits, undoing mistakes.

```
Your Computer
└── my-project/
    ├── .git/         ← this hidden folder IS the Git repository
    ├── index.html
    └── app.py
```

The `.git` folder is where Git stores your entire history. You never touch it directly — Git manages it for you.

### Remote Repository

This is a copy of your project stored **on a server** (GitHub, GitLab, Bitbucket).
It acts as a central backup that your whole team can access.

```
GitHub / GitLab
└── your-username/my-project   ← same project, stored online
```

### How Local and Remote Work Together

```
Your Computer (local)          GitHub (remote)
──────────────────────         ──────────────────────
Write code                     Stores the shared copy
Make commits                   Team members pull from here
git push  ──────────────────→  Sends your commits up
git pull  ←──────────────────  Gets new commits from others
```

| Action | Command | What it does |
|--------|---------|-------------|
| Send your work to remote | `git push` | Uploads your commits to GitHub |
| Get others' work from remote | `git pull` | Downloads new commits to your machine |
| Copy a remote repo to your machine | `git clone` | Makes a local copy of an online repo |

> **Remember:** You can work entirely on your local repo without a remote.
> A remote is only needed when you want to back up your work or share it with others.

---

## 3. Git Fundamentals

### git init — Start a New Repository

`git init` turns any folder on your computer into a Git repository.
Run it once at the start of a new project.

```bash
# Step 1: Create a new project folder
mkdir my-project

# Step 2: Go into it
cd my-project

# Step 3: Tell Git to start tracking this folder
git init
```

Output:

```
Initialized empty Git repository in /home/student/my-project/.git/
```

Git creates a hidden `.git` folder inside your project. That folder contains your entire history.

```bash
# See the hidden .git folder
ls -la
```

Output:

```
drwxr-xr-x  3 student student 4096 May 10 10:00 .
drwxr-xr-x 12 student student 4096 May 10 10:00 ..
drwxr-xr-x  7 student student 4096 May 10 10:00 .git
```

> **Important:** Never delete the `.git` folder. Deleting it removes your entire project history.

---

### git add — Stage Your Changes

Before Git saves a snapshot, you have to tell it **which files** to include in that snapshot.
This is called **staging**.

```
Working Directory         Staging Area          Repository
(files you edited)   →   (files ready to       (saved snapshots
                          be committed)          = commits)
        git add                  git commit
```

```bash
# Stage one specific file
git add index.html

# Stage multiple files
git add index.html app.py

# Stage everything in the current folder
git add .
```

> **Why does staging exist?**
> Imagine you changed 5 files but only want to save 3 of them in this commit.
> Staging lets you choose exactly what goes into each snapshot.

Check what is staged and what is not:

```bash
git status
```

Output:

```
On branch main

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   index.html      ← staged — will be included in next commit

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
        modified:   app.py          ← changed but NOT staged yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        notes.txt                   ← Git has never seen this file before
```

**Reading git status:**

| Status | Meaning |
|--------|---------|
| `Changes to be committed` | Staged — will be saved in the next commit |
| `Changes not staged` | Modified but not staged — will NOT be in the next commit |
| `Untracked files` | New files Git has never tracked before |

---

### git commit — Save a Snapshot

Once your files are staged, `git commit` saves them as a permanent snapshot in your history.

```bash
# Commit with a message describing what you did
git commit -m "Add homepage and navigation bar"
```

Output:

```
[main a3f2c91] Add homepage and navigation bar
 1 file changed, 24 insertions(+)
 create mode 100644 index.html
```

> **What makes a good commit message?**
>
> | Bad message | Good message |
> |------------|-------------|
> | `fix` | `Fix login button not responding on mobile` |
> | `update` | `Update nginx config to enable gzip compression` |
> | `changes` | `Add user registration form with validation` |
>
> Write it like you are telling a colleague what you did and why.

**The two-step process every time:**

```bash
# Step 1: Stage the files you want to save
git add .

# Step 2: Save the snapshot with a message
git commit -m "Your message here"
```

---

### git log — View Your Commit History

`git log` shows you every commit that has been made, from newest to oldest.

```bash
git log
```

Output:

```
commit a3f2c91d8e4b5f6c7d8e9f0a1b2c3d4e5f6a7b8   ← commit hash (unique ID)
Author: Taimoor <taimoor@example.com>
Date:   Sat May 10 10:30:00 2025 +0500

    Add homepage and navigation bar                ← your commit message

commit 9b1c2d3e4f5a6b7c8d9e0f1a2b3c4d5e6f7a8b9
Author: Taimoor <taimoor@example.com>
Date:   Sat May 10 10:15:00 2025 +0500

    Initial commit — add project structure
```

> **What is a commit hash?**
> Every commit gets a unique ID — a long string of letters and numbers called a **hash**.
> You use the first 7 characters of this hash to refer to a specific commit.
> Example: `a3f2c91` refers to the commit above.

**Cleaner one-line view:**

```bash
# Show each commit on a single line — easier to read
git log --oneline
```

Output:

```
a3f2c91 Add homepage and navigation bar
9b1c2d3 Initial commit — add project structure
```

**Useful git log options:**

```bash
# Show last 5 commits only
git log --oneline -5

# Show which files changed in each commit
git log --oneline --stat

# Show a visual graph (useful when working with branches)
git log --oneline --graph --all
```

**See exactly what changed in a specific commit:**

```bash
# Replace with any commit hash from your log
git show a3f2c91
```

Output shows the commit details and every line that was added (`+`) or removed (`-`).

---

### git revert — Undo a Commit Safely

`git revert` is the **safe** way to undo a commit that has already been saved.

It does not delete the bad commit — instead it creates a **new commit** that undoes the changes.
Your history stays complete and honest.

```
Before revert:
  commit 3 — broke the login page   ← you want to undo this
  commit 2 — add user profile page
  commit 1 — initial commit

After git revert:
  commit 4 — Revert "broke the login page"   ← new commit that undoes commit 3
  commit 3 — broke the login page            ← still in history (safe, honest)
  commit 2 — add user profile page
  commit 1 — initial commit
```

```bash
# Step 1: Find the hash of the commit you want to undo
git log --oneline
```

Output:

```
a3f2c91 broke the login page      ← you want to undo this one
9b1c2d3 add user profile page
4e5f6a7 initial commit
```

```bash
# Step 2: Revert it — use the first 7 characters of its hash
git revert a3f2c91
```

Git opens a text editor showing a default message like `Revert "broke the login page"`.
Save and close the editor to confirm.

```bash
# If you want to skip the editor and use the default message automatically
git revert a3f2c91 --no-edit
```

```bash
# Step 3: Verify the revert was added to your history
git log --oneline
```

Output:

```
f1a2b3c Revert "broke the login page"   ← new commit undoing the damage
a3f2c91 broke the login page
9b1c2d3 add user profile page
4e5f6a7 initial commit
```

> **git revert vs deleting the commit — what is the difference?**
>
> | | `git revert` | Deleting/rewriting history |
> |---|---|---|
> | History preserved | Yes — old commit stays | No — history is rewritten |
> | Safe to use when others have your code | Yes | No — breaks everyone else's copy |
> | Creates a new commit | Yes | No |
> | **When to use** | **Almost always** | Only on private, un-shared branches |

---

## 4. Lab: Hands-On Practice

### Task 1: Create a Repository and Make Your First Commit

```bash
# Step 1: Create a new folder for your project
mkdir git-practice
cd git-practice

# Step 2: Initialise Git
git init

# Step 3: Tell Git who you are (only needed once per machine)
git config --global user.name "Your Name"
git config --global user.email "you@example.com"

# Step 4: Create a file
echo "Hello, Git!" > readme.txt

# Step 5: Check what Git sees
git status
# You will see readme.txt listed as an untracked file

# Step 6: Stage it
git add readme.txt

# Step 7: Check status again — it is now staged
git status

# Step 8: Commit it
git commit -m "Initial commit — add readme"

# Step 9: View your first commit in the log
git log --oneline
```

### Task 2: Build a Commit History

```bash
# Make a second change and commit it
echo "Line 2: learning Git" >> readme.txt
git add readme.txt
git commit -m "Add second line to readme"

# Make a third change and commit it
echo "Line 3: this is fun" >> readme.txt
git add readme.txt
git commit -m "Add third line to readme"

# View your full history — you should see 3 commits
git log --oneline
```

Output:

```
c7d8e9f Add third line to readme
b5c6d7e Add second line to readme
a3f2c91 Initial commit — add readme
```

```bash
# See exactly what changed in your second commit (use your actual hash)
git show b5c6d7e
```

### Task 3: Revert a Change

```bash
# Step 1: Look at your history and note the hash of the third commit
git log --oneline

# Step 2: Revert that commit (replace with your actual hash)
git revert c7d8e9f --no-edit

# Step 3: Confirm the revert commit was added
git log --oneline
# You should now see 4 commits — the revert is at the top

# Step 4: Check the file — line 3 should be gone
cat readme.txt
```

Expected output of `cat readme.txt`:

```
Hello, Git!
Line 2: learning Git
```

Line 3 is gone — but your history is intact. The revert commit proves what happened and when.

---

## 5. Incident: Lost/Overwritten Changes — Recover with reflog and reset

### Scenario

A student is practicing Git. They run a `git reset --hard` command they found online trying to undo some work. Their terminal shows:

```
HEAD is now at a3f2c91 Initial commit — add readme
```

They check the log:

```bash
git log --oneline
```

Output:

```
a3f2c91 Initial commit — add readme
```

**Two commits have vanished.** The work from the last two commits is gone — or so it seems.

---

### What Happened?

`git reset --hard` moves the branch pointer backwards in history and **deletes all uncommitted changes**.

```
Before reset:
  c7d8e9f Add third line     ← HEAD was here
  b5c6d7e Add second line
  a3f2c91 Initial commit

After git reset --hard a3f2c91:
  a3f2c91 Initial commit     ← HEAD is now here
  (b5c6d7e and c7d8e9f are no longer reachable through git log)
```

The commits are not actually deleted yet — Git keeps them for 30 days before cleaning them up. You can get them back using **reflog**.

---

### What is git reflog?

`git reflog` is Git's **safety net**. It records every single thing you do to your repository — every commit, every reset, every checkout — even actions that changed or moved HEAD.

> Think of `git log` as your project history.
> Think of `git reflog` as your *personal action history* — every move you made.

```bash
git reflog
```

Output:

```
a3f2c91 HEAD@{0}: reset: moving to a3f2c91      ← the reset that caused the problem
c7d8e9f HEAD@{1}: commit: Add third line         ← your lost commit is here!
b5c6d7e HEAD@{2}: commit: Add second line        ← and this one too
a3f2c91 HEAD@{3}: commit (initial): Initial commit
```

The lost commits are still there — Git never actually deleted them. You can see their hashes.

---

### Step-by-Step Recovery

**Step 1: Find the commit you want to recover**

```bash
git reflog
# Look for the commit just before the reset
# In this example: c7d8e9f — "Add third line"
```

**Step 2: Recover by resetting back to that commit**

```bash
# This moves HEAD forward again to your lost commit
git reset --hard c7d8e9f
```

Output:

```
HEAD is now at c7d8e9f Add third line
```

**Step 3: Verify your work is back**

```bash
git log --oneline
```

Output:

```
c7d8e9f Add third line      ← recovered
b5c6d7e Add second line     ← recovered
a3f2c91 Initial commit
```

```bash
cat readme.txt
```

Output:

```
Hello, Git!
Line 2: learning Git
Line 3: this is fun
```

All your work is back.

---

### Understanding git reset — Three Modes

`git reset` has three different modes. Knowing the difference prevents accidents.

```bash
# Mode 1: --soft
# Moves HEAD back but keeps your changes staged (ready to re-commit)
git reset --soft HEAD~1

# Mode 2: --mixed (this is the DEFAULT if you type nothing)
# Moves HEAD back and unstages your changes (files still changed on disk)
git reset HEAD~1

# Mode 3: --hard   ← THE DANGEROUS ONE
# Moves HEAD back AND deletes all changes from your files
# This is what caused the incident above
git reset --hard HEAD~1
```

> **What does `HEAD~1` mean?**
> - `HEAD` = your current position (the latest commit)
> - `HEAD~1` = one commit before HEAD
> - `HEAD~3` = three commits before HEAD

| Mode | Moves HEAD | Unstages files | Deletes file changes |
|------|-----------|---------------|---------------------|
| `--soft` | Yes | No | No |
| `--mixed` (default) | Yes | Yes | No |
| `--hard` | Yes | Yes | **Yes — destructive** |

> **Rule:** If you are not sure which mode to use, avoid `--hard`.
> Use `git revert` instead — it is always safe.

---

### Incident Summary

| Step | What happened | Command used |
|------|--------------|-------------|
| 1 | Student ran `git reset --hard` — two commits seemed lost | `git reset --hard a3f2c91` |
| 2 | Checked log — only 1 commit visible | `git log --oneline` |
| 3 | Opened reflog — found the lost commit hashes | `git reflog` |
| 4 | Reset back to the lost commit | `git reset --hard c7d8e9f` |
| 5 | Verified all work restored | `git log --oneline`, `cat readme.txt` |

---

### When to Use reflog

| Situation | How reflog helps |
|-----------|-----------------|
| Ran `git reset --hard` by mistake | Find the old HEAD in reflog and reset back to it |
| Deleted a branch with unmerged commits | Find the branch tip in reflog and recreate the branch |
| Committed to the wrong branch | Find the commit in reflog and cherry-pick it |
| Lost work after a bad merge | Use reflog to find the pre-merge state |

> **Golden rule:** If you think you have lost commits in Git, check `git reflog` before panicking.
> Git almost never truly deletes your work — it just becomes temporarily hidden.

---

## Summary

### The Git Workflow — Every Time

```
1. Make changes to your files
2. git status          → see what changed
3. git add .           → stage the files you want to save
4. git commit -m "..."  → save the snapshot with a clear message
5. git log --oneline   → confirm it was saved
```

### Quick Reference — All Commands Covered

| Goal | Command |
|------|---------|
| Start a new Git repo | `git init` |
| Check what has changed | `git status` |
| Stage one file | `git add filename` |
| Stage everything | `git add .` |
| Save a snapshot | `git commit -m "message"` |
| View commit history | `git log --oneline` |
| See what a commit changed | `git show <hash>` |
| Safely undo a commit | `git revert <hash> --no-edit` |
| View every action you ever took | `git reflog` |
| Move HEAD (soft — keep staged) | `git reset --soft HEAD~1` |
| Move HEAD (mixed — unstage) | `git reset HEAD~1` |
| Move HEAD (hard — delete changes) | `git reset --hard HEAD~1` |
| Send commits to GitHub | `git push` |
| Get commits from GitHub | `git pull` |
| Copy a remote repo locally | `git clone <url>` |

