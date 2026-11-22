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

---

## What is Jenkins?

**Jenkins** is a free, **open-source automation server**. It is the most widely used tool for building CI/CD pipelines.

| Fact | Detail |
|------|--------|
| Cost | Free and open source |
| Written in | Java (so it needs Java installed) |
| Runs on | Linux, Windows, macOS, Docker, Kubernetes |
| Interface | Web browser — default port **8080** |
| Extended with | **Plugins** (over 1,800 available) |
| Pipelines written in | A file called **`Jenkinsfile`** (Groovy-based syntax) |
| History | Started in 2004 as "Hudson", renamed Jenkins in 2011 |

**What Jenkins can do:**
- Pull code from GitHub / GitLab / Bitbucket
- Build apps (Java, Node.js, Python, Go...)
- Run tests and show reports
- Build and push **Docker images**
- Deploy to servers, **AWS**, Kubernetes
- Run scheduled jobs (like cron) — backups, reports, cleanup
- Send notifications (email, Slack)

---

## How Jenkins Works (Architecture)

Jenkins has two main parts: the **controller** and **agents**.

```
                     ┌────────────────────────────────┐
  Developer ──push──►│           GitHub               │
                     └──────────────┬─────────────────┘
                                    │ webhook ("new code!")
                                    ▼
                     ┌────────────────────────────────┐
  You (browser) ───► │   Jenkins CONTROLLER  :8080    │
                     │  - web UI                      │
                     │  - stores jobs, logs, config   │
                     │  - decides what runs where     │
                     └───────┬───────────────┬────────┘
                             │               │  sends work
                             ▼               ▼
                     ┌──────────────┐ ┌──────────────┐
                     │   AGENT 1    │ │   AGENT 2    │
                     │ Linux+Docker │ │   Windows    │
                     │ runs builds  │ │ runs builds  │
                     └──────────────┘ └──────────────┘
```

| Part | Old name | Job |
|------|----------|-----|
| **Controller** | Master | The "brain" — web UI, job settings, schedules work, stores history |
| **Agent** | Slave / Node | A "worker" machine that actually runs the build steps |
| **Executor** | — | A slot on a machine that can run **one** build at a time (2 executors = 2 builds at once) |

> **For learning**, one Ubuntu machine is enough. The controller has a **built-in node** that runs builds itself.
> In real companies, builds run on **separate agents** so the controller stays fast and safe.

---

## Important Jenkins Words

| Word | Meaning | Example |
|------|---------|---------|
| **Job / Project / Item** | A task you configure in Jenkins | "build-smit-app" |
| **Build** | One **run** of a job, numbered #1, #2, #3... | Build #14 |
| **Pipeline** | A job made of **stages** written as code | Build → Test → Deploy |
| **Stage** | One big step of a pipeline | `stage('Test')` |
| **Step** | One single command inside a stage | `sh 'npm test'` |
| **Jenkinsfile** | A file in your Git repo that defines the pipeline | `Jenkinsfile` |
| **Workspace** | The folder where Jenkins checks out code and runs the build | `/var/lib/jenkins/workspace/build-smit-app` |
| **Console Output** | The full log of a build | Shows every command and its output |
| **Artifact** | A file a build produces and keeps | `app.jar`, `report.html` |
| **Trigger** | What starts a build | Git push, schedule, manual click |
| **Webhook** | A message GitHub sends to Jenkins when code changes | `POST /github-webhook/` |
| **Credentials** | Saved secrets (passwords, tokens, SSH keys) Jenkins uses safely | Docker Hub password |
| **Plugin** | An add-on that gives Jenkins new features | Git plugin, Docker plugin |
| **Node** | Any machine that can run builds (controller or agent) | `built-in`, `linux-agent-1` |

### Build Status Colours

| Icon | Status | Meaning |
|------|--------|---------|
| ✅ Green | **Success** | Everything passed |
| ❌ Red | **Failure** | A step failed (e.g. a command exited with non-zero code) |
| ⚠️ Yellow | **Unstable** | Build ran but some tests failed |
| ⚪ Grey | **Aborted / Not built** | Stopped by a person, or never ran |

> Jenkins decides pass/fail using **exit codes** — the same ones you learned in `Phase_3/class1.md`.
> Exit code `0` = success, anything else = failure.
