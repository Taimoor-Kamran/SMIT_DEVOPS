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