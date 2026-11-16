# Claude Code: Your AI Pair Programmer for DevOps

---

## Table of Contents
1. [What is Claude Code?](#what-is-claude-code)
2. [Chat AI vs Agentic AI](#chat-ai-vs-agentic-ai)
3. [Why DevOps Engineers Use Claude Code](#why-devops-engineers-use-claude-code)
4. [Where You Can Use Claude Code](#where-you-can-use-claude-code)
5. [What You Need (Requirements)](#what-you-need-requirements)
6. [Install Claude Code — Step by Step](#install-claude-code--step-by-step)
7. [Log In for the First Time](#log-in-for-the-first-time)
8. [Your First Session — Step by Step](#your-first-session--step-by-step)
9. [How Claude Code Works (The Agent Loop)](#how-claude-code-works-the-agent-loop)
10. [Permissions — Claude Asks Before Acting](#permissions--claude-asks-before-acting)
11. [Permission Modes](#permission-modes)
12. [CLAUDE.md — Give Claude Project Memory](#claudemd--give-claude-project-memory)
13. [Undo Mistakes with Rewind](#undo-mistakes-with-rewind)
14. [Lab: Use Claude Code on a DevOps Task](#lab-use-claude-code-on-a-devops-task)
15. [Writing Good Prompts](#writing-good-prompts)
16. [Safety Rules and Best Practices](#safety-rules-and-best-practices)
17. [Update, Diagnose and Uninstall](#update-diagnose-and-uninstall)
18. [Quick Reference](#quick-reference)

---

## What is Claude Code?

**Claude Code** is an **AI coding assistant** made by **Anthropic**.
It runs **inside your project folder** and can **read your files, write code, run commands, and fix errors** — while you watch and approve each step.

You talk to it in plain English, for example:

```
> write a bash script that backs up /var/log to /backup every night and add it to cron
```

And Claude Code will:
1. Look at your project files
2. Write the script
3. Ask your permission to save it
4. Ask your permission to run a test command
5. Tell you what it did

**Simple analogy:**
Normal ChatGPT-style chat is like **calling a friend** for help — they tell you what to type, and **you** do the typing.
Claude Code is like that friend **sitting next to you at your laptop** — they can open files and type commands themselves, but they **ask you first** before touching anything.

---

## Chat AI vs Agentic AI

| | Chat AI (normal chatbot) | Agentic AI (Claude Code) |
|---|---|---|
| Sees your files | ❌ Only what you copy-paste | ✅ Reads your project directly |
| Edits files | ❌ You copy the answer and paste it | ✅ Edits files for you (after you approve) |
| Runs commands | ❌ You run them yourself | ✅ Runs `ls`, `git`, `docker`, tests... (after you approve) |
| Fixes its own mistakes | ❌ You paste the error back | ✅ Sees the error output and tries again |
| Multi-step work | One answer at a time | Plans and finishes the whole task |

> **Agent** = an AI that can **take actions** (use tools), not just talk.
> The "tools" Claude Code uses are things like *Read file*, *Edit file*, *Run bash command*, *Search the web*.

---

## Why DevOps Engineers Use Claude Code

| DevOps task | Example prompt |
|-------------|---------------|
| Write scripts | `write a bash script that checks disk usage and emails me if it's above 80%` |
| Understand old code | `explain what deploy.sh does, step by step` |
| Fix errors | `my docker build fails with this error, fix it` |
| Write config files | `create a Dockerfile for this Node.js app using a small alpine image` |
| CI/CD | `write a GitHub Actions workflow that runs tests on every pull request` |
| Git work | `look at my changes and write a good commit message` |
| Learn | `what does chmod 750 mean? show me on this folder` |
| Review | `review this script for security problems` |

> Claude Code is a **helper, not a replacement** for learning. Always read what it writes so **you** understand it too.

---

## Where You Can Use Claude Code

Claude Code is one product that you can open in many places:

```
                         ┌────────────────────┐
                         │    Claude Code     │
                         └─────────┬──────────┘
     ┌───────────┬────────────┬────┴──────┬────────────┬────────────┐
     ▼           ▼            ▼           ▼            ▼            ▼
 Terminal    VS Code     JetBrains    Desktop app    Web         Mobile
  (CLI)     extension     plugin      (Code tab)  claude.ai/code  app
```

| Where | Best for |
|-------|----------|
| **Terminal (CLI)** | Full features, scripting, servers, WSL — ⭐ **what DevOps people use most** (see `claude-cli.md`) |
| **VS Code extension** | Working inside VS Code with diffs shown in the editor |
| **JetBrains plugin** | IntelliJ, PyCharm, WebStorm and other JetBrains IDEs |
| **Desktop app → Code tab** | Visual diff review, many sessions side by side (see `claude-desktop.md`) |
| **Web — `claude.ai/code`** | Cloud sessions that keep running even after you close your laptop |
| **Mobile app (iOS / Android)** | Start and check on cloud sessions from your phone |
| **Slack** | Mention `@Claude` in a channel to give it a task |
| **GitHub Actions / GitLab CI** | Let Claude work automatically inside your pipelines |

All of them use the **same account** and the **same settings files**.

---

## What You Need (Requirements)

### Account

You need a **paid** Claude plan **or** an API account:

| Account type | Can use Claude Code? |
|--------------|---------------------|
| Claude **Free** | ❌ No |
| Claude **Pro** | ✅ Yes — good for students and individuals |
| Claude **Max / Ultra** | ✅ Yes — more usage |
| Claude **Team / Enterprise** | ✅ Yes — for companies |
| **Claude Console** (API, pay per use) | ✅ Yes |
| Amazon Bedrock / Google Cloud / Microsoft Foundry | ✅ Yes — for companies using cloud providers |

### Computer

| Item | Minimum |
|------|---------|
| macOS | 13.0 or newer |
| Windows | Windows 10 (1809) or newer |
| Linux | Ubuntu 20.04+, Debian 10+, Alpine 3.19+ |
| RAM | 4 GB or more |
| CPU | x64 or ARM64 |
| Internet | Required |
| Shell | Bash, Zsh, PowerShell or CMD |

> **On Windows**, you can run Claude Code natively **or** inside **WSL 2** (the Ubuntu you installed in `Linux_OS/class_1`).
> For DevOps practice, **WSL 2 is recommended** — your commands will be real Linux commands.
> If you use native Windows, install **Git for Windows** first.

---

## Install Claude Code — Step by Step

Pick **one** method. The **native installer** is the recommended one — it **updates itself automatically**.

### Method 1: Native Installer (Recommended)

**macOS / Linux / WSL (Ubuntu):**

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

**Windows PowerShell:**

```powershell
irm https://claude.ai/install.ps1 | iex
```

**Windows CMD:**

```bat
curl -fsSL https://claude.ai/install.cmd -o install.cmd && install.cmd && del install.cmd
```

> **What does `curl ... | bash` mean?**
> `curl` downloads the install script, and `| bash` runs it straight away.
> Only run this kind of command from websites you **trust** (here: `claude.ai`, the official site).

### Method 2: Package Managers

| Tool | Install | Update (manual) |
|------|---------|----------------|
| Homebrew (macOS) | `brew install --cask claude-code` | `brew upgrade claude-code` |
| WinGet (Windows) | `winget install Anthropic.ClaudeCode` | `winget upgrade Anthropic.ClaudeCode` |
| npm (needs Node.js 22+) | `npm install -g @anthropic-ai/claude-code@latest` | same command again |

> ⚠️ Never use `sudo npm install -g` — it causes permission problems. Use the native installer instead if npm complains.
> Package-manager installs do **not** auto-update — you must update them yourself.

### Step 2: Check It Installed

Close and reopen your terminal, then:

```bash
claude --version
```

Output (your version number will be different):

```
2.1.325 (Claude Code)
```

If you see `command not found`, restart the terminal. Still not working? Run `claude doctor` (see [Update, Diagnose and Uninstall](#update-diagnose-and-uninstall)).

---

## Log In for the First Time

- Open a terminal and type:

```bash
claude
```

- Choose your **theme** (light / dark) with the arrow keys → **Enter**
- Choose how to log in:
  - **Claude account** (Pro / Max / Team / Enterprise) ← most students
  - **Claude Console** account (API billing)
- A **browser window** opens → sign in to your Claude account → click **Authorize**
- Go back to the terminal — you will see **"Login successful"**

**Login commands you can use any time:**

```bash
claude auth status     # am I logged in? which account?
claude auth login      # log in
claude auth logout     # log out
```

> Inside WSL, if the browser doesn't open automatically, copy the link shown in the terminal and paste it into your Windows browser.
