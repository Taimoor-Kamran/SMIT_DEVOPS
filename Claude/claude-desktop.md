# Claude Desktop App: Chat, Code and Cowork

---

## Table of Contents
1. [What is the Claude Desktop App?](#what-is-the-claude-desktop-app)
2. [The Three Modes: Chat, Code and Cowork](#the-three-modes-chat-code-and-cowork)
3. [Requirements](#requirements)
4. [Install the Desktop App — Step by Step](#install-the-desktop-app--step-by-step)
5. [Sign In and Tour the App](#sign-in-and-tour-the-app)
6. [Mode 1: Chat](#mode-1-chat)
7. [Mode 2: Code (Claude Code in the App)](#mode-2-code-claude-code-in-the-app)
8. [Mode 3: What is Cowork?](#mode-3-what-is-cowork)
9. [Start Your First Cowork Task — Step by Step](#start-your-first-cowork-task--step-by-step)
10. [Cowork Approval Modes](#cowork-approval-modes)
11. [Folder Access — How Cowork Uses Your Files](#folder-access--how-cowork-uses-your-files)
12. [Cowork Network Access](#cowork-network-access)
13. [What Else Cowork Can Do](#what-else-cowork-can-do)
14. [Connectors, Skills and Plugins](#connectors-skills-and-plugins)
15. [Cowork vs Claude Code — Which One?](#cowork-vs-claude-code--which-one)
16. [Lab: Try All Three Modes](#lab-try-all-three-modes)
17. [Safety Rules and Limitations](#safety-rules-and-limitations)
18. [Quick Reference](#quick-reference)

---

## What is the Claude Desktop App?

The **Claude Desktop app** is a normal program you install on your computer (like WhatsApp Desktop or VS Code).
It puts **all of Claude's features in one window** — chatting, coding, and doing multi-step work on your files.

**Why use the app instead of the website?**
- It can work with **files and folders on your computer**
- It runs **Claude Code** with a visual screen (no terminal needed)
- It has **Cowork** for getting real work done on documents and files
- One click from your taskbar / dock

> The website `claude.ai` and the Desktop app use the **same account** — your chats show up in both.

---

## The Three Modes: Chat, Code and Cowork

```
 ┌────────────────────────────────────────────────────────────────────┐
 │                       Claude Desktop App                           │
 ├──────────────────────┬──────────────────────┬──────────────────────┤
 │        Chat          │        Code          │       Cowork         │
 │                      │                      │                      │
 │  Ask questions,      │  Claude Code with a  │  Give an outcome —   │
 │  learn, brainstorm,  │  visual screen: edit │  Claude does multi-  │
 │  write text          │  code, see diffs,    │  step work on files, │
 │                      │  run commands        │  docs, spreadsheets  │
 │                      │                      │                      │
 │  "Explain Docker     │  "Add a health check │  "Turn these 10 PDF  │
 │   volumes"           │   to my Dockerfile"  │   invoices into one  │
 │                      │                      │   Excel sheet"       │
 └──────────────────────┴──────────────────────┴──────────────────────┘
```

| Mode | Who it's for | Works on your files? | Needs coding? |
|------|-------------|---------------------|--------------|
| **Chat** | Everyone | Only files you upload | No |
| **Code** | Developers, DevOps | ✅ Your project folder | Yes (it's Claude Code) |
| **Cowork** | Everyone — students, office work, research | ✅ Folders you connect | **No** |

**Simple analogy:**
- **Chat** = asking a teacher a question.
- **Code** = a programmer sitting with you at your laptop.
- **Cowork** = an assistant you give a job to — "organise these files and make a report" — and they come back when it's done.

---

## Requirements

### Account

| Plan | Chat | Code | Cowork |
|------|------|------|--------|
| Free | ✅ | ❌ | ❌ |
| Pro | ✅ | ✅ | ✅ |
| Max / Ultra | ✅ | ✅ | ✅ |
| Team / Enterprise | ✅ | ✅ | ✅ (admin can control it) |

### Computer

| OS | Minimum |
|----|---------|
| **macOS** | macOS 13 (Ventura) or newer |
| **Windows** | Windows 10 or newer (x64 or ARM64) |
| **Linux** *(beta)* | Ubuntu 22.04 LTS+ or Debian 12+ (x64 or arm64) |

---

## Install the Desktop App — Step by Step

### Windows

- Go to **`https://claude.ai/download`**
- Click the **Windows** download
- Open the downloaded installer file
- Follow the installer → it opens Claude when finished
- Later, open it from the **Start menu** → search **Claude**

### macOS

- Go to **`https://claude.ai/download`**
- Click the **macOS** download (a `.dmg` file)
- Open the `.dmg` → drag **Claude** into the **Applications** folder
- Open **Applications → Claude**
- If macOS asks *"Are you sure you want to open it?"* → click **Open**

### Linux (Ubuntu / Debian) — Beta

The Linux app is installed from Anthropic's **apt repository** (or a `.deb` file). Follow the setup steps on the download page to add the repository, then:

```bash
sudo apt update && sudo apt install claude-desktop
```

Updates come through apt as usual:

```bash
sudo apt update && sudo apt upgrade
```

> **WSL users:** install the Desktop app on **Windows** (not inside WSL). The Code tab can still work with your WSL projects.

---

## Sign In and Tour the App

- Open the app → click **Sign in**
- Sign in with the **same account** you use on `claude.ai` (Google, email, etc.)
- Allow any permissions macOS / Windows asks for (notifications etc.)

**What you'll see:**

```
 ┌────────────────┬─────────────────────────────────────────────┐
 │  Sidebar       │                                             │
 │                │                                             │
 │  + New         │          Main area                          │
 │  Chats/Tasks   │      (your conversation or task)            │
 │  Projects      │                                             │
 │  Scheduled     │                                             │
 │  Customize     │  ┌──────────────────────────────────────┐   │
 │                │  │ Message box    [+]  [mode]  [model]  │   │
 │  Settings      │  └──────────────────────────────────────┘   │
 └────────────────┴─────────────────────────────────────────────┘
```

| Part | What it does |
|------|-------------|
| **Sidebar** | Old conversations, projects, scheduled tasks, Customize (connectors, skills, plugins) |
| **Message box** | Where you type |
| **`+` button** | Attach files, add connectors |
| **Mode selector** | Switch between Chat / Code / Cowork |
| **Model picker** | Choose which Claude model to use |

> Anthropic updates the app often, so button names and positions can change slightly. The ideas stay the same.

---

## Mode 1: Chat

Chat is the normal "ask Claude anything" mode.

**Good uses for a DevOps student:**
- `Explain the difference between a Docker image and a container like I'm a beginner`
- `Make me a 10-question quiz on Linux file permissions`
- `What's the difference between git merge and git rebase?`
- Upload a screenshot of an error → `What does this error mean?`

**Tips:**
- Use **Projects** to group chats about one topic (e.g. "AWS Study") and add files Claude should always know about
- Chat **cannot** change files on your computer — use **Code** or **Cowork** for that

---

## Mode 2: Code (Claude Code in the App)

The **Code** tab is **Claude Code** — the same tool as the terminal `claude` command (see `claude-code.md`), but with a **visual screen**.

### Start a Code Session

- Click the **Code** tab
- Choose a **project folder** on your computer (e.g. your `SMIT_DEVOPS` repo)
- Type a task, e.g. `add a README section that explains the AWS folder`
- Claude shows each change as a **diff** (red = removed, green = added) → **approve** or **reject**

### Why Use Code in the App Instead of the Terminal?

| Desktop Code tab | Terminal CLI |
|------------------|-------------|
| Visual diff review — easier to read changes | Text diffs |
| Many sessions side by side, each with its own folder | One session per terminal window |
| No terminal knowledge needed to start | Needs terminal comfort |
| Computer use (Claude can use apps on your screen — Pro/Max/Ultra, preview) | — |
| — | Works over SSH on servers, scripts, CI/CD (`-p`) |

> Both share the same **settings**, **CLAUDE.md** and **permissions**.
> You can move a terminal session into the app with `/desktop` inside the CLI.

---

## Mode 3: What is Cowork?

**Cowork** uses the **same agent technology as Claude Code** — but with **no terminal and no coding needed**.

You describe the **result** you want, and Claude works through all the steps itself:
reading files, creating documents, building spreadsheets with formulas, making presentations, researching the web — often running several **sub-agents** at the same time.

**Examples of Cowork tasks:**

| You say | Cowork does |
|---------|------------|
| `Organise my Downloads folder into subfolders by file type` | Looks at every file, creates folders, moves files |
| `Read all the PDFs in this folder and make an Excel sheet of invoice number, date and amount` | Reads each PDF, builds the spreadsheet with totals |
| `Turn my Phase_1 notes into a 10-slide revision presentation` | Reads the notes, writes and designs the slides |
| `Research the top 5 DevOps tools for 2026 and write a 2-page comparison` | Searches the web, writes a document with sources |
| `Every Monday, summarise my week's class notes` | Runs on a schedule |

**Simple analogy:**
Chat is **asking for directions**. Cowork is **handing someone the car keys** and saying "please pick up these three things and bring them home" — they plan the route and do it.

---

## Start Your First Cowork Task — Step by Step

### Step 1: Open Cowork

- Open the **Claude Desktop app** (Cowork also works on `claude.ai` and the mobile app)
- In the message box, select **Cowork**

> If you don't see separate **Chat** and **Cowork** options, you have the newer **unified** version of the app —
> any conversation can take a Cowork task. Just describe the job.

### Step 2: Connect a Folder (for File Tasks)

- If the task uses files on your computer, **connect the folder** Claude should work in when the app asks
- Pick a **specific** folder — e.g. `Documents/SMIT-Notes` — **not** your whole home folder or C: drive

### Step 3: Describe the Task Clearly

Example:

```
In the folder SMIT-Notes, read all the .md files in Phase_1.
Create a file called Phase_1_Revision.docx with:
- one heading per class
- 5 key points per class
- a 10-question quiz at the end with answers on the last page
```

### Step 4: Review Claude's Plan

Cowork shows **how it plans to do the task** before it starts the heavy work.
- Read it
- If something is wrong, tell it: `don't include the incident sections`

### Step 5: Let it Run and Approve Actions

- Claude works step by step and shows progress
- Depending on the **approval mode** (next section), it asks you to **Allow** or **Deny** actions
- You can keep using your computer while it works

### Step 6: Check the Result

- Open the files Cowork created and check them
- Ask for changes: `make the quiz harder` or `add page numbers`

---

## Cowork Approval Modes

Use the **mode selector** in the message box to choose how much Claude asks you:

| Mode | What happens | Use when |
|------|-------------|----------|
| **Manually approve** | Claude asks **Allow / Deny** for each action | ✅ Beginners, sensitive files, first time trying a task |
| **Automatically approve** | A **safety reviewer** checks each action and blocks unsafe ones; you're not asked every time | Normal everyday tasks (default on Pro, Max, Ultra, Team) |
| **Skip all approvals** | **Nothing** checks actions | ⚠️ Only fully trusted tasks on unimportant files |

> On **Enterprise** plans, **Manually approve** is the default, and admins can turn off the other modes.
> **Deleting files** always needs a separate, explicit **Allow** from you.

---

## Folder Access — How Cowork Uses Your Files

- Cowork can **read and write** files only in folders **you connect**
- For local files, the **Desktop app must be open** and connected
- If you **rename or move** a connected folder, reconnect it from its new location
  (on Apple-silicon Macs the app may offer **"Keep connected"** for the new location)

```
 Your computer
 ├── Documents/
 │   ├── SMIT-Notes/     ✅ connected → Cowork can read & write here
 │   └── Bank/           ❌ not connected → Cowork can't see it
 └── Downloads/          ❌ not connected
```

> **Least privilege again!** Just like AWS IAM (`AWS/02_iam_user_groups.md`), give Cowork access
> to the **smallest folder** it needs. Keep a **backup** (or use Git) before big file-organising tasks.

---

## Cowork Network Access

When Cowork runs commands, its **internet access** is limited by a setting in **Settings → Capabilities**:

| Setting | Cowork can reach |
|---------|-----------------|
| **Default** (network egress off) | Only Anthropic's own sites |
| **Package managers only** | Plus common package registries (npm, PyPI, etc.) and GitHub |
| **All domains** | Any website |

> Some hosts like npm and GitHub are **download-only** — Cowork can install packages, but it **can't upload**.
> For example, `git push` is blocked. Push your code from your own terminal.
> These limits are helpful guard-rails, **not** a complete security boundary.

---

## What Else Cowork Can Do

| Feature | What it does |
|---------|-------------|
| **Scheduled tasks** | Run a task automatically on a schedule — type `/schedule` in a task, or use **Scheduled** in the sidebar |
| **Projects** | Group related tasks and files together |
| **Browser use** | Claude can open web pages in its built-in browser (or Claude in Chrome if you use it) — change this in **Settings → Cowork** |
| **Plugins** | Add ready-made packs of skills and tools for a job role |
| **Dispatch** *(Pro, Max, Ultra)* | Send a task from your **phone** and let your desktop do the work |
| **Sub-agents** | Splits big jobs into parts that run at the same time |

---

## Connectors, Skills and Plugins

Open **Customize** in the sidebar to manage these:

| Feature | What it is | Example |
|---------|-----------|---------|
| **Connectors** | Connect Claude to apps you use (built on **MCP**) | Google Drive, Gmail, GitHub, Slack, Notion |
| **Skills** | Saved instructions for a type of task — load automatically or run with `/` | "Write notes in SMIT beginner style" |
| **Plugins** | Bundles of skills, connectors and tools | A ready-made "sales" or "design" toolkit |

**Add a connector:**
- Click **`+`** next to the message box → **Add connectors**
- Choose the app → sign in → allow access

> Cowork **does not** read the `~/.claude` folder that the Claude Code CLI uses.
> Skills you made for the CLI must be added separately in **Customize** for Cowork.

> ⚠️ A connector gives Claude access to that app's data. Only connect accounts you're comfortable sharing, and remove connectors you don't use.

---

## Cowork vs Claude Code — Which One?

| | Cowork | Claude Code |
|---|---|---|
| Made for | Everyday knowledge work: files, documents, research | Software and DevOps work: code, scripts, servers |
| Needs terminal / coding | ❌ No | ✅ Helpful |
| Output | Documents, spreadsheets, presentations, organised files | Code changes, commits, scripts, configs |
| Where it runs | Desktop app, web, mobile, Chrome | Terminal, IDEs, Desktop Code tab, web |
| Runs in | An isolated environment on Anthropic's servers, using folders you connect | Directly on your machine (or in the cloud on the web) |
| Git push / deploy | ❌ Blocked (download-only network) | ✅ Yes (with your permission) |
| Automation in CI/CD | ❌ | ✅ `claude -p` |

**Rule of thumb:**
- Building or fixing **code / infrastructure** → **Claude Code**
- Producing **documents, reports, organised files** → **Cowork**
- Just **learning or asking** → **Chat**

---

## Lab: Try All Three Modes

### Part 1 — Chat
- Ask: `Explain the 4 SDLC environments DEV, SIT, UAT, PROD with a real example`
- Then: `Make a 5-question quiz on that`

### Part 2 — Code
- Open the **Code** tab → choose a practice folder (e.g. `~/claude-practice`)
- Ask: `create a bash script that prints the top 5 processes by memory usage`
- Review the diff → approve → ask Claude to run it

### Part 3 — Cowork
- Make a test folder `Cowork-Practice` and put a few files in it (some `.txt`, `.png`, `.pdf` files)
- Open **Cowork** → choose **Manually approve**
- Connect the `Cowork-Practice` folder
- Ask: `Organise this folder into subfolders by file type and create a summary.md listing what moved where`
- Approve each step and check the result

**Check yourself:**
- [ ] You can explain when to use Chat vs Code vs Cowork
- [ ] You saw a permission / approval request and understood it
- [ ] Cowork only touched the `Cowork-Practice` folder

---

## Safety Rules and Limitations

### Safety Rules

| Rule | Why |
|------|-----|
| Connect only the **folder** a task needs | Least privilege — less risk if something goes wrong |
| Start with **Manually approve** | You see every action while you learn |
| **Back up** files before big organising jobs | Mistakes on many files are hard to undo |
| Read the **plan** before letting it run | Catch misunderstandings early |
| Don't connect folders with **passwords, keys or bank documents** | Keep sensitive data away from AI tasks |
| Only add **trusted** connectors and plugins | They can read and act on your data |
| Check the **output** before sending it to anyone | You're responsible for the final work |

> Cowork runs in an **isolated environment** on Anthropic's servers. That protects your computer itself,
> but it does **not** limit what Claude can reach through the folders, connectors and permissions **you** give it.

### Current Limitations

- The Cowork desktop experience is in **beta** — features change often
- **Sessions can't be shared** with others (individual created files / artifacts can)
- **`git push` and uploads** are blocked from Cowork's network
- Some features (like plugins that use local MCP servers) work **only in the desktop app**, not on the web
- When you delete a task, it disappears from your history right away and is removed from Anthropic's storage within 30 days

---

## Quick Reference

### Desktop App Cheat Sheet

| Task | How |
|------|-----|
| Download | `https://claude.ai/download` |
| Linux install | `sudo apt install claude-desktop` (after adding the repo) |
| Start a chat | Message box → **Chat** |
| Start coding | **Code** tab → choose project folder |
| Start a Cowork task | Message box → **Cowork** → describe the result |
| Change approval mode | Mode selector in the message box |
| Add a connector | **`+`** → **Add connectors** |
| Manage skills / plugins | Sidebar → **Customize** |
| Schedule a task | `/schedule` in a task, or **Scheduled** in the sidebar |
| Network access for Cowork | **Settings → Capabilities** |
| Move a CLI session into the app | `/desktop` inside the terminal `claude` |

### Which Mode? Cheat Sheet

| I want to... | Use |
|--------------|-----|
| Learn a concept / ask a question | Chat |
| Write or fix a script, Dockerfile, pipeline | Code |
| Review code changes visually | Code |
| Turn notes into slides or a Word document | Cowork |
| Organise or rename lots of files | Cowork |
| Build a spreadsheet from PDFs | Cowork |
| Run something every week automatically | Cowork scheduled task |

### Key Terms Glossary

| Term | Meaning |
|------|---------|
| Claude Desktop | Anthropic's installable app for macOS, Windows and Linux |
| Chat | Normal question-and-answer conversation mode |
| Code tab | Claude Code inside the Desktop app with a visual screen |
| Cowork | Agent mode for multi-step work on files and documents, no coding needed |
| Approval mode | How often Cowork asks you before acting (Manual, Auto, Skip) |
| Connected folder | A folder on your computer that Cowork is allowed to use |
| Connector | A link between Claude and another app, built on MCP |
| Skill | Saved instructions for a type of task |
| Plugin | A bundle of skills, connectors and tools |
| Scheduled task | A Cowork task that runs automatically on a schedule |
| Dispatch | Sending a task from your phone to your desktop |
| Sub-agent | A helper that handles one part of a bigger task in parallel |
| Network egress | Cowork's ability to reach the internet |
| Beta | A feature that works but is still being improved |
