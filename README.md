# SMIT DevOps — Beginner Class Notes

> My complete **DevOps learning notes** from the **Saylani Mass IT Training (SMIT)** DevOps course —
> written in simple English, step by step, so that **any beginner** can learn DevOps from zero.

![Level](https://img.shields.io/badge/level-beginner-brightgreen)
![Topics](https://img.shields.io/badge/topics-Linux%20%7C%20Git%20%7C%20Bash%20%7C%20Python%20%7C%20Docker%20%7C%20AWS-blue)
![Format](https://img.shields.io/badge/format-Markdown-lightgrey)

---

## Table of Contents
1. [What is This Repo?](#what-is-this-repo)
2. [Why This Repo Exists](#why-this-repo-exists)
3. [Who Should Use It?](#who-should-use-it)
4. [What You Will Learn](#what-you-will-learn)
5. [Learning Path (Recommended Order)](#learning-path-recommended-order)
6. [Folder Structure](#folder-structure)
7. [Notes Index](#notes-index)
8. [How Every Note is Written](#how-every-note-is-written)
9. [How to Use This Repo](#how-to-use-this-repo)
10. [Tools You Need](#tools-you-need)
11. [Tips for Beginners](#tips-for-beginners)
12. [Contributing / Found a Mistake?](#contributing--found-a-mistake)
13. [Author](#author)

---

## What is This Repo?

This repository is a collection of **class notes** for learning **DevOps** — from your very first Linux command all the way to Docker containers and AWS cloud security.

Every topic taught in class is written down as a **Markdown (`.md`) file** with:
- simple explanations and real-life analogies
- commands you can copy and run
- step-by-step labs (practice)
- real-world **incidents** (something breaks → how to find and fix it)
- cheat sheets for quick revision

**Simple analogy:**
Think of this repo as a **notebook you never lose**. Instead of paper notes that get lost after the course, everything lives on GitHub — searchable, organised, and free for anyone to read.

---

## Why This Repo Exists

| Problem | How this repo helps |
|---------|---------------------|
| Class moves fast and you forget commands | Every command is written with an explanation of **what** it does and **why** |
| Online tutorials assume you already know things | Notes start from **zero** and explain every new word |
| Theory without practice is easy to forget | Each class has a **Lab** so you actually type the commands |
| Real jobs are about fixing problems, not just running commands | Many classes include an **Incident** section — debugging like a real DevOps engineer |
| Revision before interviews takes time | Every note ends with a **Quick Reference / Cheat Sheet** |

> **Goal:** a beginner should be able to open this repo, follow it in order, and understand DevOps fundamentals **without any other resource**.

---

## Who Should Use It?

- 🎓 **SMIT students** — revise what was taught in class
- 🐣 **Complete beginners** — no Linux, Git or cloud experience needed
- 🔄 **People switching careers** into DevOps, Cloud or SRE
- 💼 **Job seekers** — quick revision before DevOps interviews
- 👨‍💻 **Developers** who want to understand servers, CI/CD, containers and the cloud

**You do NOT need:** a computer science degree, prior coding experience, or a paid cloud account (AWS Free Tier is enough).

---

## What You Will Learn

| Area | Topics |
|------|--------|
| **DevOps Basics** | What DevOps is, SDLC phases, environments (DEV → SIT → UAT → PROD) |
| **Linux** | WSL setup, filesystem, paths, file commands, searching, permissions, users & groups, processes, signals, tuning, logs, disk, networking |
| **Remote Servers** | SSH keys, connecting to servers, creating an AWS EC2 instance, VirtualBox VMs |
| **Git & GitHub** | Git basics, local vs remote, branching, trunk-based development, feature flags, pull requests, code review, recovering lost work |
| **CI/CD** | GitHub Actions workflows, PR checks, merge policies, branch protection |
| **Scripting** | Bash variables, arguments, exit codes, loops, conditionals, cron jobs, service scripts, Python for files / JSON / APIs |
| **Containers** | Virtual machines vs containers, Docker images, image layers, Docker commands |
| **AWS Cloud** | IAM users, user groups, policies, custom policies, root account MFA, IAM user MFA |
| **Web Basics** | HTML tags, text formatting, lists |

---

## Learning Path (Recommended Order)

Follow the folders in this order. Each step builds on the one before it.

```
 ┌──────────────┐    ┌──────────────┐    ┌──────────────┐    ┌──────────────┐
 │  Linux_OS    │──► │   Phase_1    │──► │   Phase_2    │──► │   Phase_3    │
 │ Linux basics │    │ DevOps + SSH │    │  Git, PRs,   │    │ Bash + Python│
 │ & commands   │    │ Linux admin  │    │ GitHub CI/CD │    │  scripting   │
 └──────────────┘    └──────────────┘    └──────────────┘    └──────┬───────┘
                                                                    │
                     ┌──────────────┐    ┌──────────────┐           │
                     │     AWS      │◄── │   Phase_4    │◄──────────┘
                     │ IAM, Users,  │    │   Docker &   │
                     │   MFA        │    │  Containers  │
                     └──────────────┘    └──────────────┘

   HTML  →  optional side track (web page basics), read any time
```

| Step | Folder | Why at this point |
|------|--------|-------------------|
| 1 | `Linux_OS/` | Almost every server in the world runs Linux — learn the terminal first |
| 2 | `Phase_1/` | DevOps concepts + real Linux administration (users, permissions, processes, logs) |
| 3 | `Phase_2/` | Git and GitHub — how teams store code and work together |
| 4 | `Phase_3/` | Automate the boring stuff with Bash and Python |
| 5 | `Phase_4/` | Package apps into containers with Docker |
| 6 | `AWS/` | Move to the cloud and secure your AWS account |
| — | `HTML/` | Optional — basics of how web pages are built |

---

## Folder Structure

```
SMIT_DEVOPS/
├── Linux_OS/        # Linux basics: WSL, commands, permissions, users, networking, shell scripting
│   ├── class_1/ ... class_8/
├── Phase_1/         # DevOps fundamentals, SSH, Linux administration, processes, logs, disk, networking
│   ├── class1.md ... class_7.md
├── Phase_2/         # Git, branching, pull requests, code review, GitHub Actions
│   ├── class1.md ... class4.md
├── Phase_3/         # Bash scripting, services & logs, loops & cron, Python for DevOps
│   ├── class1.md ... class4.md
├── Phase_4/         # Containers vs VMs, Docker images and layers
│   └── class1.md
├── AWS/             # AWS IAM users, groups, policies and MFA
│   ├── 01_iam_users.md ... 04_iam_users_mfa.md
├── HTML/            # HTML basics
│   ├── class_1/ class_2/
└── README.md        # You are here
```

---

## Notes Index

### 🐧 Linux_OS — Linux Basics

| Class | Topic |
|-------|-------|
| [Class 1](Linux_OS/class_1/notes.md) | What is DevOps, installing WSL and Ubuntu on Windows |
| [Class 2](Linux_OS/class_2/notes.md) | Basic commands — `pwd`, `ls`, `mkdir`, `cd`, `touch`, `cat` and more |
| [Class 3](Linux_OS/class_3/notes.md) | Searching files (`locate`, `find`, `grep`), help commands, viewing & comparing files |
| [Class 4](Linux_OS/class_4/notes.md) | `chmod`, `chgrp`, downloads, system info, text processing, date & calendar |
| [Class 5](Linux_OS/class_5/notes.md) | User management — add, modify, delete users, passwords, expiry & lock |
| [Class 6](Linux_OS/class_6/nodes.md) | Red Hat Linux, SNAP package manager, SNAP vs APT |
| [Class 7](Linux_OS/class_7/nodes.md) | Networking commands — `ip`, `ping`, `traceroute`, `netstat`, `dig`, `curl`, `scp` |
| [Class 8](Linux_OS/class_8/nodes.md) | Shell scripting — shells, variables, input, exit status, operators |

### 🚀 Phase 1 — DevOps Fundamentals & Linux Administration

| Class | Topic |
|-------|-------|
| [Class 1](Phase_1/class1.md) | DevOps overview, SDLC, environments, SSH keys, AWS EC2, VirtualBox VM |
| [Class 2](Phase_1/class2.md) | Linux filesystem, paths, permissions basics, core commands |
| [Class 3](Phase_1/class_3.md) | Users, groups, `chmod`, `chown`, `umask`, file ownership for services |
| [Class 4](Phase_1/class_4.md) | Service users, directory ownership, secure permissions |
| [Class 5](Phase_1/class_5.md) | Processes and signals — `ps`, `top`, `htop`, `kill` |
| [Class 6](Phase_1/class_6.md) | Basic tuning — heavy processes, simulating load, nice values, safe termination |
| [Class 7](Phase_1/class_7.md) | Logs, disk (`df`, `du`, inodes), networking (IP, DNS, HTTP), cloud networking intro |

### 🌿 Phase 2 — Git, GitHub & CI/CD

| Class | Topic |
|-------|-------|
| [Class 1](Phase_2/class1.md) | Git fundamentals, local vs remote, recovering lost changes with `reflog` |
| [Class 2](Phase_2/class2.md) | Branching, trunk-based development, feature flags, rebase |
| [Class 3](Phase_2/class3.md) | Pull request workflow, code review, environment promotion |
| [Class 4](Phase_2/class4.md) | GitHub Actions — PR checks, merge policies, branch protection |

### 🛠️ Phase 3 — Scripting & Automation

| Class | Topic |
|-------|-------|
| [Class 1](Phase_3/class1.md) | Bash scripting basics — variables, arguments, exit codes |
| [Class 2](Phase_3/class2.md) | Start/stop services with `systemctl` and tail logs |
| [Class 3](Phase_3/class3.md) | Advanced Bash — loops, conditionals, cron jobs, monitoring script |
| [Class 4](Phase_3/class4.md) | Python for DevOps — files, JSON, API requests, reports |

### 🐳 Phase 4 — Containers & Docker

| Class | Topic |
|-------|-------|
| [Class 1](Phase_4/class1.md) | Containers vs VMs, Docker images, image layers, Docker commands |

### ☁️ AWS — Cloud & Security

| # | Topic |
|---|-------|
| [01](AWS/01_iam_users.md) | IAM users — create users, sign-in, account alias, access keys, password policy |
| [02](AWS/02_iam_user_groups.md) | User groups, policies, custom policies, allow vs deny, Policy Simulator |
| [03](AWS/03_root_account_mfa.md) | Root account MFA and other AWS authentication methods |
| [04](AWS/04_iam_users_mfa.md) | MFA for IAM users, enforcing MFA, MFA with the AWS CLI |

### 🌐 HTML — Web Basics

| Class | Topic |
|-------|-------|
| [Class 1](HTML/class_1/nodes.md) | HTML structure, headings, paragraphs, text formatting, quotations |
| [Class 2](HTML/class_2/notes.md) | `abbr`, `cite`, `address`, `ins`/`del`, ordered, unordered & description lists |

---

## How Every Note is Written

Most notes follow the same pattern, so once you read one, you know how to read all of them:

| Section | What it gives you |
|---------|-------------------|
| **Title + Table of Contents** | See the whole class at a glance |
| **Concept explanation** | What it is, in plain English, often with a real-life analogy |
| **Diagrams** | ASCII pictures that show how things connect |
| **Commands with comments** | Copy-paste ready, every line explained |
| **Tables** | Comparisons (e.g. VM vs Container, Root vs IAM user) |
| **`>` Notes and ⚠️ warnings** | Common mistakes and important tips |
| **Lab** | Hands-on practice — do it yourself |
| **Incident** | A real problem → how to investigate → how to fix |
| **Quick Reference / Glossary** | Cheat sheet for revision and interviews |

---

## How to Use This Repo

### Option 1: Read on GitHub (easiest)

Just click any link in the [Notes Index](#notes-index). GitHub shows Markdown files nicely formatted.

### Option 2: Download to Your Computer

```bash
git clone https://github.com/Taimoor-Kamran/SMIT_DEVOPS.git
cd SMIT_DEVOPS
```

Open the folder in **VS Code** and press `Ctrl + Shift + V` on any `.md` file to see the formatted preview.

Get the latest notes later with:

```bash
git pull
```

### Study Method That Works

1. **Read** the concept part of a class
2. **Type** every command yourself (don't just copy-paste — typing builds memory)
3. **Do the Lab** at the end of the class
4. **Break something on purpose** and fix it, like the Incident section
5. **Revise** using the Quick Reference before moving to the next class

---

## Tools You Need

| Tool | Why | Cost |
|------|-----|------|
| Linux terminal — **Ubuntu** / **WSL** on Windows | Run all Linux and Bash commands | Free |
| **VS Code** | Read notes and write scripts | Free |
| **Git** + **GitHub account** | Phase 2 and version control | Free |
| **Python 3** | Phase 3 Python scripts | Free |
| **Docker** (Docker Desktop or Docker Engine) | Phase 4 containers | Free |
| **AWS account** | EC2 and AWS folder | Free Tier |
| **Authenticator app** (Google / Microsoft Authenticator) | AWS MFA | Free |
| **VirtualBox** *(optional)* | Run an Ubuntu virtual machine | Free |

> ⚠️ **AWS tip:** set a **billing budget alert** as soon as you create your account,
> and always **stop or terminate** EC2 instances after practice so you don't get charged.

---

## Tips for Beginners

- **Don't rush.** One class properly understood is better than five classes skimmed.
- **Errors are normal.** Read the error message — it usually tells you exactly what is wrong.
- **Use `man` and `--help`.** Example: `man ls` or `ls --help`.
- **Practise daily**, even 30 minutes. DevOps is a hands-on skill.
- **Never share secrets** — passwords, `.pem` keys, or AWS access keys must never be pushed to GitHub.
- **Build small projects** — a backup script, a Dockerised app, a GitHub Actions pipeline.

---

## Contributing / Found a Mistake?

These are learning notes, so improvements are welcome!

1. **Fork** this repository
2. Create a branch: `git checkout -b fix/typo-in-phase1`
3. Make your change and commit: `git commit -m "Fix typo in Phase 1 class 2"`
4. Push: `git push origin fix/typo-in-phase1`
5. Open a **Pull Request**

Or simply open an **Issue** describing what's wrong or what topic you'd like added.

> If these notes helped you, please give the repo a ⭐ — it helps other beginners find it.

---

## Author

**Taimoor Kamran**
DevOps student at **Saylani Mass IT Training (SMIT)**

- GitHub: [@Taimoor-Kamran](https://github.com/Taimoor-Kamran)

*Learning in public — one class at a time.* 🚀
