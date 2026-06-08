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

## Setting Up a Workflow File

All GitHub Actions workflows live in a special folder:

```
your-repo/
└── .github/
    └── workflows/
        └── ci.yml       ← your workflow file goes here
```

**Step-by-step for beginners:**

### Step 1 — Create the folders
```bash
mkdir -p .github/workflows
```

### Step 2 — Create your first workflow file `ci.yml`
```yaml
name: CI Pipeline          # Name shown in GitHub Actions tab

on:                        # TRIGGER: when does this run?
  push:                    # run on every push
    branches: [ main ]     # only on the main branch
  pull_request:            # also run when a PR is opened
    branches: [ main ]

jobs:                      # JOBS: what to do
  build-and-test:          # job name (you can name it anything)
    runs-on: ubuntu-latest # use a Linux server to run the job

    steps:                 # STEPS: one-by-one commands

      - name: Checkout code              # Step 1: download your code
        uses: actions/checkout@v4        # built-in GitHub action

      - name: Set up Node.js             # Step 2: install Node (if needed)
        uses: actions/setup-node@v4
        with:
          node-version: '20'

      - name: Install dependencies       # Step 3: npm install
        run: npm install

      - name: Run tests                  # Step 4: run your tests
        run: npm test

      - name: Validate code style        # Step 5: lint check
        run: npm run lint
```

> **Beginner tip:** Every line after `run:` is a normal terminal command.
> `uses:` means "use a pre-built action from the GitHub marketplace."

### Step 3 — Commit and push the file
```bash
git add .github/workflows/ci.yml
git commit -m "Add CI workflow"
git push origin main
```
After pushing, go to your repo on GitHub → click the **Actions** tab → you will see the workflow running!

---
