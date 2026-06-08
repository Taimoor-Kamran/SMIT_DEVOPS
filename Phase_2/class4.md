# Class 4: GitHub Actions — PR, Checks & Merge Policies

---

## Table of Contents
1. [What is GitHub Actions?](#what-is-github-actions)
2. [Setting Up a Workflow File](#setting-up-a-workflow-file)
3. [Opening a Pull Request (PR)](#opening-a-pull-request-pr)
4. [Running Checks (CI Pipeline)](#running-checks-ci-pipeline)
5. [Merge Policies & Branch Protection](#merge-policies--branch-protection)
6. [Incident: Merge Conflict + Failed Validation](#incident-merge-conflict--failed-validation)
7. [Quick Reference](#quick-reference)

---

## What is GitHub Actions?

GitHub Actions is a **free automation tool** built into GitHub.
It lets you automatically run tasks (like tests, builds, deployments)
whenever something happens in your repository — for example, when you
push code or open a Pull Request.

**Key terms for beginners:**

| Term | Simple Meaning |
|------|---------------|
| Workflow | A set of automatic tasks written in a YAML file |
| Job | A group of steps inside a workflow |
| Step | One single command or action inside a job |
| Runner | The server/machine that runs your workflow |
| Trigger (on:) | The event that starts the workflow (e.g. push, pull_request) |

Think of it like this:
- You push code → GitHub sees it → GitHub automatically runs your tests → tells you pass or fail.

---
