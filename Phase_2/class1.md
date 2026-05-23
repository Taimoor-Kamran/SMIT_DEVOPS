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
