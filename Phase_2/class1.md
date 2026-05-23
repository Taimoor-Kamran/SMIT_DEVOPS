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
