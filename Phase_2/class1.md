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
