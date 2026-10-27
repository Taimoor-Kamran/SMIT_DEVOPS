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
