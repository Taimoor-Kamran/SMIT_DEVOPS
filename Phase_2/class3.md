# Class 3: PR Workflow, Code Review & Environment Promotion

---

## 1. What is a Pull Request?

A **Pull Request (PR)** is a formal way of saying: "I have finished my work on this branch — please review it and merge it into main."

It is not a Git command — it is a feature provided by platforms like **GitHub**, **GitLab**, and **Azure DevOps**.

### Real-World Example

Imagine you are working at a company. You have finished building a new user login page on your branch `feature/login`.

You do NOT push directly to `main`. Instead, you open a Pull Request.

A PR creates a page where your teammates can:
- See exactly which lines of code you changed
- Leave comments and ask questions
- Approve or request changes
- Let automated checks (tests, linters) run before anything is merged

### The Full PR Lifecycle

```
Developer                    Platform (GitHub)             Reviewer
    |                              |                           |
    |-- push branch -------------> |                           |
    |-- open PR ------------------> |                           |
    |                              |-- notify reviewers ------> |
    |                              |<-- review comments ------- |
    |<-- fix requested changes --- |                           |
    |-- push new commits --------> |                           |
    |                              |-- run checks (CI) -------> |
    |                              |<-- approve --------------- |
    |                              |-- merge to main            |
```

---

## 2. Code Review

**Code review** is the process where a teammate reads your code before it is merged — looking for bugs, security issues, unclear logic, or anything that does not follow team standards.

> Think of it like proofreading an essay before submitting — a second pair of eyes catches things you missed.

### What a Reviewer Looks For

| Category | Example |
|----------|---------|
| **Correctness** | Does the code do what it says it does? |
| **Security** | Is user input validated? Any SQL injection risk? |
| **Readability** | Are variable names clear? Is logic easy to follow? |
| **Tests** | Does new code have tests covering it? |
| **Standards** | Does it follow the team's style guide? |

### Review Actions on GitHub

When you finish reviewing a PR on GitHub, you choose one of three outcomes:

| Action | Meaning |
|--------|---------|
| **Approve** | Code looks good — ready to merge |
| **Request Changes** | Found issues — the developer must fix before merging |
| **Comment** | Left notes or questions, but did not block the merge |

### Branch Protection Rules

Most teams set **branch protection rules** on `main` to enforce quality before any merge happens.

Common rules:
- At least 1 reviewer must approve before merging
- All automated checks (CI pipeline) must pass
- Direct pushes to `main` are blocked — everything must go through a PR
- Conversation threads must be resolved before merge

---

## 3. Environment Promotion — DEV → SIT → UAT → PROD

In professional software teams, code does not go straight from a developer's laptop to production (live users).

It travels through a series of **environments** — each one a copy of the application, used for a different purpose.

```
Code change → DEV → SIT → UAT → PROD
                ↑     ↑     ↑      ↑
           Developer  QA  Business  Real users
             tests   test  sign-off
```

### DEV — Development Environment