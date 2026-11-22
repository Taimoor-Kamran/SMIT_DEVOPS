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
