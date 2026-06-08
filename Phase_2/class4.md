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

## Opening a Pull Request (PR)

A **Pull Request** is a formal way to say:
> "I made changes on my branch — please review and merge them into main."

### Full Workflow (step by step)

#### 1. Create a new branch (never code directly on main)
```bash
git checkout -b feature/add-login-page
# "feature/add-login-page" is the branch name — name it based on what you're building
```

#### 2. Make your changes and commit them
```bash
# edit your files...
git add .
git commit -m "Add login page HTML and CSS"
```

#### 3. Push the branch to GitHub
```bash
git push origin feature/add-login-page
```

#### 4. Open the PR on GitHub
- Go to your repo on github.com
- You will see a yellow banner: **"Compare & pull request"** → click it
- Fill in:
  - **Title**: short description (e.g. `Add login page`)
  - **Description**: what you changed and why
- Click **"Create pull request"**

#### 5. What happens next?
As soon as you open the PR, GitHub Actions **automatically** starts running
your workflow (because we set `on: pull_request` in `ci.yml`).

```
PR opened
   ↓
GitHub triggers ci.yml workflow
   ↓
Checks run (tests, lint, build)
   ↓
  PASS ✅  →  PR is ready to review & merge
  FAIL ❌  →  you must fix the code before merging
```

---

## Running Checks (CI Pipeline)

When a PR is opened, GitHub runs all the **checks** defined in your workflow.
You can see them at the bottom of the PR page under **"Checks"**.

### What checks look like in the PR

```
Checks
├── build-and-test
│   ├── ✅ Checkout code
│   ├── ✅ Install dependencies
│   ├── ✅ Run tests
│   └── ❌ Validate code style   ← this step failed
```

If any step fails, the whole job is marked **failed** and GitHub blocks the merge
(if branch protection is enabled — covered next).

### How to see detailed logs
1. On the PR page, scroll down to **Checks** section
2. Click the failing check name
3. Click the failing step to expand the log
4. Read the error message — it tells you exactly what went wrong

### Re-running checks manually
If a check failed due to a flaky test or network issue (not your code), you can re-run:
1. Click **Actions** tab → find the failed run
2. Click **"Re-run all jobs"** or **"Re-run failed jobs"**

Or from the PR page:
- Scroll to the checks section → click **"Re-run failed checks"**

> **Beginner tip:** Always read the log before re-running. Re-running does NOT
> fix a real code problem — it only helps with random/flaky failures.

---

## Merge Policies & Branch Protection

**Branch protection rules** are settings you put on the `main` branch so that
nobody (not even you) can merge broken or unreviewed code.

### How to enable branch protection on GitHub
1. Go to your repo → **Settings** → **Branches**
2. Click **"Add branch protection rule"**
3. Set **Branch name pattern** to `main`
4. Enable these options:

| Setting | What it does |
|---------|-------------|
| Require a pull request before merging | No one can push directly to main |
| Require status checks to pass | All CI checks must be green before merge |
| Require branches to be up to date | Your branch must have the latest main code |
| Require approvals (1 or 2) | Someone else must review and approve your PR |
| Do not allow bypassing the above settings | Even admins must follow the rules |

5. Click **"Save changes"**

### What happens when rules are in place?
```
Developer pushes to main directly
   ↓
GitHub BLOCKS it — "Push rejected: branch is protected"

Developer opens a PR with failing checks
   ↓
GitHub BLOCKS merge — "Required status checks have not passed"

Developer opens a PR, checks pass, gets approval
   ↓
GitHub ALLOWS merge ✅
```

---

## Incident: Merge Conflict + Failed Validation

This is a **real-world scenario** that every developer faces. Let's walk through it completely.

### What is a Merge Conflict?

A merge conflict happens when **two people edit the same line** in the same file on different branches, and Git does not know which version to keep.

**Example scenario:**
```
main branch has:    greeting = "Hello"

Person A changes:   greeting = "Hi there"   (on branch feature/A)
Person B changes:   greeting = "Welcome"    (on branch feature/B)

Person A merges first → no problem
Person B tries to merge → CONFLICT! Git sees two different changes to the same line
```

### What a conflict looks like in your file

When a conflict happens, Git adds markers into the file:

```
<<<<<<< HEAD                    ← your current branch starts here
greeting = "Welcome"
=======                         ← separator
greeting = "Hi there"
>>>>>>> main                    ← what's in main ends here
```

You must manually choose which version to keep (or combine both), then remove the markers.

### Step-by-Step: Resolve a Merge Conflict

#### Step 1 — Update your branch with latest main
```bash
git checkout feature/your-branch   # switch to your branch
git fetch origin                   # download latest changes from GitHub
git merge origin/main              # try to merge main into your branch
```
If there are conflicts, Git will say:
```
CONFLICT (content): Merge conflict in app.js
Automatic merge failed; fix conflicts and then commit the result.
```

#### Step 2 — Find all conflicted files
```bash
git status
# Files with "both modified" are the ones with conflicts
```

#### Step 3 — Open each conflicted file and fix it
```bash
# open the file in your editor
# find the <<<<<<< markers
# decide which code to keep
# DELETE the marker lines: <<<<<<<, =======, >>>>>>>
# save the file
```

**Before fix:**
```javascript
<<<<<<< HEAD
const greeting = "Welcome";
=======
const greeting = "Hi there";
>>>>>>> main
```

**After fix (you chose to keep your version):**
```javascript
const greeting = "Welcome";
```

#### Step 4 — Mark the conflict as resolved and commit
```bash
git add app.js                        # stage the fixed file
git commit -m "Resolve merge conflict in app.js"
git push origin feature/your-branch  # push the fix to GitHub
```

### Failed Validation — What it means and how to fix it

After resolving the conflict and pushing, GitHub re-runs the CI pipeline automatically.
But the checks may **still fail** if your code has errors.

**Common reasons checks fail:**

| Reason | Example error message | Fix |
|--------|-----------------------|-----|
| Syntax error | `SyntaxError: Unexpected token` | Fix the typo in your code |
| Test failure | `FAIL src/app.test.js` | Read the test log, fix the failing test |
| Lint error | `error: 'var' is not allowed` | Run `npm run lint -- --fix` locally |
| Missing dependency | `Cannot find module 'express'` | Run `npm install` and commit `package-lock.json` |
| Wrong Node version | `Engine "node" is incompatible` | Match Node version in workflow to your project |

### How to fix a failed validation

#### Step 1 — Read the error log
On GitHub → Actions tab → click the failed run → expand the failed step.

#### Step 2 — Reproduce the error locally
```bash
# Run the same command that failed in CI
npm test          # if tests failed
npm run lint      # if lint failed
npm run build     # if build failed
```

#### Step 3 — Fix the code
```bash
# edit the file to fix the error
git add .
git commit -m "Fix lint error in app.js"
git push origin feature/your-branch
```

#### Step 4 — Pipeline re-runs automatically
As soon as you push, GitHub sees the new commit and **automatically re-runs** all checks.
You do NOT need to manually trigger it.

```
Push fix → GitHub detects new commit → CI re-runs → All checks pass ✅ → PR ready to merge
```

---
