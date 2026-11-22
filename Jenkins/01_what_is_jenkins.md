# Jenkins: What is Jenkins and CI/CD?

---

## Table of Contents
1. [The Problem Jenkins Solves](#the-problem-jenkins-solves)
2. [What is CI/CD?](#what-is-cicd)
3. [What is Jenkins?](#what-is-jenkins)
4. [How Jenkins Works (Architecture)](#how-jenkins-works-architecture)
5. [Important Jenkins Words](#important-jenkins-words)
6. [Types of Jenkins Jobs](#types-of-jenkins-jobs)
7. [What are Plugins?](#what-are-plugins)
8. [Jenkins vs GitHub Actions vs Others](#jenkins-vs-github-actions-vs-others)
9. [Where Jenkins Fits in DevOps](#where-jenkins-fits-in-devops)
10. [Quick Reference](#quick-reference)

---

## The Problem Jenkins Solves

Imagine a team of 5 developers working on one app. **Without automation**, every release looks like this:

```
 Developer pushes code
        │
        ▼
 Someone logs in to the server by SSH       ← manual
        │
        ▼
 git pull, install packages, build          ← manual, easy to forget a step
        │
        ▼
 Run tests (if they remember)               ← often skipped
        │
        ▼
 Restart the app and hope it works          ← "it worked on my machine!"
```

**Problems:**
- Slow — someone has to do it by hand every time
- Mistakes — a forgotten step breaks production
- Bugs found late — tests aren't run on every change
- Nobody knows **who** deployed **what** and **when**

**With Jenkins**, a `git push` starts all of these steps **automatically**, the same way every time, and keeps a log of every run.

---

## What is CI/CD?

**CI/CD** is the practice of **automatically building, testing and deploying** code every time it changes.

| Term | Full form | Meaning |
|------|-----------|---------|
| **CI** | Continuous Integration | Every push is automatically **built and tested**, so bugs are found within minutes |
| **CD** | Continuous Delivery | The tested code is always **ready to release**; a person clicks "deploy" |
| **CD** | Continuous Deployment | Tested code is deployed to production **automatically**, with no click |

```
  Code ──► Build ──► Test ──► Package ──► Deploy DEV ──► Deploy SIT/UAT ──► Deploy PROD
  └──────────── CI ────────────┘   └──────────────────── CD ───────────────────────┘
```

> Remember the environments from `Phase_1/class1.md`: **DEV → SIT → UAT → PROD**.
> A CI/CD pipeline moves code through them automatically.

**Simple analogy:**
CI/CD is like a **factory assembly line**. Raw material (your code) goes in at one end. Machines (stages) shape it, check it, and package it. A finished, tested product comes out the other end — the same way every time.
