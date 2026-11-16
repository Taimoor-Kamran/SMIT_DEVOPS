# Claude Code CLI: Commands, Flags, Slash Commands and Shortcuts

---

## Table of Contents
1. [What is the Claude CLI?](#what-is-the-claude-cli)
2. [Two Ways to Use the CLI](#two-ways-to-use-the-cli)
3. [Starting Claude — Basic Commands](#starting-claude--basic-commands)
4. [Resume Old Conversations](#resume-old-conversations)
5. [Important Flags (Options)](#important-flags-options)
6. [Slash Commands (Inside a Session)](#slash-commands-inside-a-session)
7. [Keyboard Shortcuts](#keyboard-shortcuts)
8. [Special Prefixes: `/`, `@`, `!`](#special-prefixes---)
9. [Managing Context (Claude's Short-Term Memory)](#managing-context-claudes-short-term-memory)
10. [Print Mode — Claude in Scripts and Pipelines](#print-mode--claude-in-scripts-and-pipelines)
11. [Settings Files](#settings-files)
12. [Permission Rules (Allow / Ask / Deny)](#permission-rules-allow--ask--deny)
13. [MCP — Connect Claude to Other Tools](#mcp--connect-claude-to-other-tools)
14. [Skills, Subagents, Hooks and Plugins](#skills-subagents-hooks-and-plugins)
15. [The `.claude` Folder Map](#the-claude-folder-map)
16. [Lab: CLI Practice](#lab-cli-practice)
17. [Troubleshooting](#troubleshooting)
18. [Quick Reference](#quick-reference)

---

## What is the Claude CLI?

**CLI** stands for **Command Line Interface** — a program you use by typing in the terminal instead of clicking buttons.

The **Claude CLI** is the `claude` command. It is **Claude Code running in your terminal**.

> If you haven't installed Claude Code yet, read `claude-code.md` first.

**Why the CLI matters for DevOps:**
- Servers usually have **no graphical screen** — only a terminal over SSH
- The CLI can run **inside scripts, cron jobs and CI/CD pipelines**
- It has the **full feature set** — every other version of Claude Code is built on top of it

---

## Two Ways to Use the CLI

```
  ┌──────────────────────────────────┐        ┌──────────────────────────────────┐
  │  1. Interactive mode             │        │  2. Print mode (-p)              │
  │                                  │        │                                  │
  │  $ claude                        │        │  $ claude -p "explain this log"  │
  │  > you type...                   │        │  (prints the answer and exits)   │
  │  < Claude answers...             │        │                                  │
  │  > you type again...             │        │  Used in: scripts, cron,         │
  │                                  │        │  pipelines, GitHub Actions       │
  │  A live chat in the terminal     │        │  One question → one answer       │
  └──────────────────────────────────┘        └──────────────────────────────────┘
```

| | Interactive | Print (`-p`) |
|---|---|---|
| How to start | `claude` | `claude -p "question"` |
| Conversation | Back and forth | One shot, then exits |
| Permission prompts | Shown on screen | Not possible — you decide permissions with flags |
| Best for | Daily coding and learning | Automation and scripts |

---

## Starting Claude — Basic Commands

| Command | What it does |
|---------|-------------|
| `claude` | Start an interactive session in the current folder |
| `claude "explain this project"` | Start interactive and send the first message straight away |
| `claude -p "explain this project"` | Print mode — answer once and exit |
| `cat error.log \| claude -p "what is wrong?"` | Send a file's content into Claude through a pipe |
| `claude -c` | **Continue** the most recent conversation in this folder |
| `claude -r` | **Resume** — choose an old conversation from a list |
| `claude --version` | Show the installed version |
| `claude update` | Update to the latest version |
| `claude doctor` | Check installation health |
| `claude auth login` / `logout` / `status` | Manage login |
| `claude mcp ...` | Manage MCP servers (see [MCP](#mcp--connect-claude-to-other-tools)) |
| `claude plugin ...` | Manage plugins |
| `claude --help` | Show every command and flag |

> `claude --help` on **your** machine is always the final truth — new flags are added often.

---

## Resume Old Conversations

Claude saves every session. You can come back to them later.

```bash
claude -c                       # continue the last conversation in this folder
claude -c -p "now add tests"    # continue it in print mode
claude -r                       # open a list of old sessions and pick one
claude -r "docker-fix"          # resume the session named "docker-fix"
claude -n "docker-fix"          # start a new session and give it a name
```

Inside a session:
- `/resume` → pick another old session
- `/rename docker-fix` → give the current session a name, so it's easy to find later

---

## Important Flags (Options)

A **flag** changes how `claude` starts. Example: `claude --model opus`.

| Flag | What it does | Example |
|------|-------------|---------|
| `-p`, `--print` | Print mode (answer and exit) | `claude -p "summarise README.md"` |
| `-c`, `--continue` | Continue the last session | `claude -c` |
| `-r`, `--resume` | Resume a chosen session | `claude -r` |
| `-n`, `--name` | Name the session | `claude -n "nginx-setup"` |
| `--model` | Choose the model (`sonnet`, `opus`, `haiku`, `fable`, or a full model ID) | `claude --model sonnet` |
| `--effort` | How hard Claude thinks (more effort = slower but more careful) | `claude --effort high` |
| `--permission-mode` | Start in a mode: `default`, `acceptEdits`, `plan`, `auto`, `dontAsk`, `bypassPermissions` | `claude --permission-mode plan` |
| `--add-dir` | Let Claude also work in another folder | `claude --add-dir ../shared-scripts` |
| `--allowedTools` | Tools/commands allowed **without asking** | `claude --allowedTools "Bash(git status)" "Read"` |
| `--disallowedTools` | Tools/commands that are **blocked** | `claude --disallowedTools "Bash(rm *)"` |
| `--output-format` | Print mode output: `text`, `json`, `stream-json` | `claude -p "hi" --output-format json` |
| `--max-turns` | Stop after N steps (print mode) | `claude -p "fix lint" --max-turns 5` |
| `--max-budget-usd` | Stop if the cost goes above a dollar limit (print mode) | `claude -p "..." --max-budget-usd 1` |
| `--append-system-prompt` | Add extra instructions for this run | `claude --append-system-prompt "Answer in simple English"` |
| `--mcp-config` | Load MCP servers from a JSON file | `claude --mcp-config mcp.json` |
| `--settings` | Load settings from a file | `claude --settings ci-settings.json` |
| `--chrome` | Turn on the Claude in Chrome browser integration | `claude --chrome` |
| `--desktop` | Open this session in the Claude Desktop app | `claude --desktop` |
| `--dangerously-skip-permissions` | Never ask for permission | ⚠️ **Only** in throw-away containers |

> ⚠️ **`--dangerously-skip-permissions`** lets Claude run **any** command without asking — including deleting files.
> The word "dangerously" is there for a reason. Only use it inside a disposable Docker container or VM with no secrets.

---

## Slash Commands (Inside a Session)

Inside an interactive session, type `/` to see a menu of commands. Start typing to filter the list.

### Session and Context

| Command | What it does |
|---------|-------------|
| `/clear` | Start a fresh conversation (empty context) |
| `/compact` | Summarise the conversation to free up space (you can add instructions: `/compact keep the docker errors`) |
| `/context` | Show how full Claude's context is |
| `/resume` | Open an old session |
| `/rename <name>` | Rename the current session |
| `/rewind` | Go back to a checkpoint (undo edits) |
| `/exit` | Quit Claude Code |

### Settings

| Command | What it does |
|---------|-------------|
| `/config` | Open the settings menu |
| `/model` | Change the AI model |
| `/effort` | Change how much effort Claude uses |
| `/permissions` | View and edit allow / ask / deny rules |
| `/memory` | Edit CLAUDE.md files and auto memory |
| `/theme` | Change colours |
| `/keybindings` | Customise keyboard shortcuts |
| `/statusline` | Set up the status line at the bottom |

### Project Work

| Command | What it does |
|---------|-------------|
| `/init` | Create a `CLAUDE.md` for this project |
| `/plan <task>` | Switch to plan mode and plan a task |
| `/add-dir <path>` | Add another folder to the session |
| `/diff` | Show the changes made in this session |
| `/review` | Review code changes |
| `/security-review` | Look for security problems in your changes |

### Tools and Extensions

| Command | What it does |
|---------|-------------|
| `/mcp` | Manage MCP server connections |
| `/agents` | Manage subagents |
| `/skills` | List available skills |
| `/hooks` | View hooks |
| `/plugin` | Browse and install plugins |

### Help and Account

| Command | What it does |
|---------|-------------|
| `/help` | Show help and all commands |
| `/status` | Show version, account, model and settings |
| `/usage` | Show how much of your plan usage you've used |
| `/doctor` | Check and fix installation problems |
| `/login` / `/logout` | Switch or sign out of your account |
| `/feedback` | Report a bug to Anthropic |
| `/release-notes` | See what's new |

> Type `/help` to see the full, up-to-date list on your version.

---

## Keyboard Shortcuts

### Most Important

| Key | What it does |
|-----|-------------|
| `Enter` | Send your message |
| `Esc` | **Stop** Claude (keeps the work done so far) |
| `Esc` `Esc` | Clear the prompt — or, if the prompt is empty, open **rewind** |
| `Shift + Tab` | Switch permission mode (Default → Accept edits → Plan → ...) |
| `Ctrl + C` | Interrupt; press twice to exit |
| `Ctrl + D` | Exit (press twice) |
| `?` | Show shortcut help (on an empty prompt) |

### Useful Extras

| Key | What it does |
|-----|-------------|
| `↑` / `↓` | Scroll through your previous messages |
| `Ctrl + R` | Search your message history |
| `Ctrl + O` | Open the full transcript viewer |
| `Ctrl + T` | Show / hide Claude's task checklist |
| `Ctrl + B` | Background tasks |
| `Ctrl + G` | Open your prompt in a text editor (good for long prompts) |
| `Ctrl + S` | Save the current prompt for later (stash) |
| `Ctrl + L` | Redraw the screen |
| `Ctrl + V` (`Alt + V` on Windows / WSL) | Paste an image (e.g. a screenshot of an error) |
| `Alt + P` (`Option + P` on Mac) | Switch model |
| `Alt + T` (`Option + T` on Mac) | Turn extended thinking on/off |

> **Multi-line messages:** press `Shift + Enter` (or type `\` then `Enter`) to add a new line without sending.
> On macOS, `Option` shortcuts need **"Use Option as Meta key"** turned on in your terminal settings.

---

## Special Prefixes: `/`, `@`, `!`

| Start your message with | Meaning | Example |
|-------------------------|---------|---------|
| `/` | Run a slash command or skill | `/init` |
| `@` | Mention a file or folder (with autocomplete) | `explain @scripts/backup.sh` |
| `!` | **Shell mode** — run the command yourself; the output is added to the chat | `!docker ps` |

**Example using `!`:**

```
> !docker ps -a
CONTAINER ID   IMAGE    STATUS
a1b2c3d4e5f6   nginx    Exited (1) 2 minutes ago

> why did my nginx container exit? check its logs
```

Claude now knows which container you mean and can check its logs.

---

## Managing Context (Claude's Short-Term Memory)

**Context** is everything Claude currently "remembers" in this session — your messages, files it read, command outputs.
It has a **size limit** (called the **context window**). When it gets full, Claude becomes slower and less accurate.

**Simple analogy:** context is like a **whiteboard**. You can only fit so much. When it's full, either **erase it** (`/clear`) or **summarise** what's on it (`/compact`).

| Situation | What to do |
|-----------|-----------|
| Starting a **new, unrelated** task | `/clear` |
| Long task, want to keep the important parts | `/compact` |
| Want to see how full it is | `/context` |
| Want something remembered **forever** | Put it in `CLAUDE.md` |

> Claude also compacts **automatically** when the context is nearly full, so long sessions keep working.

---

## Print Mode — Claude in Scripts and Pipelines

Print mode (`-p`) turns Claude into a normal Linux command, so you can use it with pipes `|`, redirects `>` and scripts.

### Examples

```bash
# Explain a log file
cat /var/log/nginx/error.log | claude -p "explain these errors in simple words"

# Save the answer to a file
claude -p "write a .gitignore for a Python project" > .gitignore

# Explain the last 5 commits
git log -5 --stat | claude -p "summarise these commits for a status report"

# Review staged changes before committing
git diff --staged | claude -p "review this diff for bugs and security problems"

# Get JSON output for other tools
claude -p "list the services in docker-compose.yml" --output-format json
```

### Print Mode in a Bash Script

```bash
#!/bin/bash
# disk_report.sh — ask Claude to explain disk usage

set -euo pipefail

report=$(df -h; du -sh /var/log/* 2>/dev/null | sort -rh | head -10)

echo "$report" | claude -p "Explain this disk usage report for a beginner. Which folders should I clean first?" \
  --max-turns 3
```

### Permissions in Print Mode

Print mode **can't show permission pop-ups**, so you decide in advance with flags:

```bash
# Allow only read-only git commands and reading files
claude -p "check git status and tell me what's not committed" \
  --allowedTools "Bash(git status)" "Bash(git diff *)" "Read"
```

> Anything not allowed is simply **refused**, and Claude tells you it couldn't do it.
> In CI/CD (GitHub Actions, GitLab CI) you also use print mode, with an **API key** stored as a **secret**.

---

## Settings Files

Claude Code reads settings from **JSON files**. There are several levels:

| File | Applies to | Commit to Git? |
|------|-----------|---------------|
| `~/.claude/settings.json` | **You**, in all projects | — (personal) |
| `.claude/settings.json` | **Everyone** on this project | ✅ Yes — shared with the team |
| `.claude/settings.local.json` | **You**, only in this project | ❌ No — keep out of Git |
| Managed settings | Whole company (set by IT) | — |

**Which one wins?** If two files disagree, the more specific one wins:

```
 Managed (company)            ← highest
   │
 --settings flag
   │
 .claude/settings.local.json  (you, this project)
   │
 .claude/settings.json        (team, this project)
   │
 ~/.claude/settings.json      (you, everywhere)  ← lowest
```

> The easy way to change settings is the `/config` command — it writes these files for you.

---

## Permission Rules (Allow / Ask / Deny)

Instead of answering the same permission question every time, write **rules** in a settings file.

```json
{
  "permissions": {
    "allow": [
      "Bash(git status)",
      "Bash(git diff *)",
      "Bash(npm run test *)",
      "Bash(docker ps)"
    ],
    "ask": [
      "Bash(git push *)"
    ],
    "deny": [
      "Read(./.env)",
      "Read(./.env.*)",
      "Bash(rm -rf *)"
    ]
  }
}
```

| List | Meaning |
|------|---------|
| `allow` | Do it **without asking** |
| `ask` | **Always ask** first |
| `deny` | **Never** allow — blocked |

**How rules are written:**

| Rule | Matches |
|------|---------|
| `Bash(git status)` | Exactly the command `git status` |
| `Bash(npm run test *)` | `npm run test` followed by anything |
| `Read(./.env)` | Reading the `.env` file |
| `Edit` | Any file edit |

> Just like AWS IAM policies (`AWS/02_iam_user_groups.md`), **deny always wins** over allow.
> A good habit: **deny** reading `.env` files so secrets never get sent to the AI.

You can also manage these rules with the `/permissions` command inside a session.

---

## MCP — Connect Claude to Other Tools

**MCP** stands for **Model Context Protocol**. It's a standard way to plug **extra tools** into Claude — like GitHub, databases, a web browser, Jira or Slack.

**Simple analogy:** MCP is like **USB** for AI. Any tool that has an "MCP plug" can connect to Claude.

```
                 ┌──────────────┐
                 │ Claude Code  │
                 └──────┬───────┘
                        │ MCP
      ┌─────────────────┼─────────────────┐
      ▼                 ▼                 ▼
 ┌─────────┐      ┌──────────┐      ┌──────────┐
 │ GitHub  │      │ Database │      │ Browser  │
 └─────────┘      └──────────┘      └──────────┘
```

### Add an MCP Server

```bash
# A remote server over HTTP
claude mcp add --transport http <name> <url>

# A local server program (everything after -- is the command to run)
claude mcp add playwright -- npx -y @playwright/mcp@latest
```

### Scopes — Who Gets the Server?

| Flag | Saved for |
|------|----------|
| `--scope local` (default) | Only you, only this project |
| `--scope user` | Only you, all projects |
| `--scope project` | Everyone on the project (saved in `.mcp.json`, commit it) |

### Manage Servers

```bash
claude mcp list            # list all servers
claude mcp get playwright  # details of one server
claude mcp remove playwright
```

Inside a session, `/mcp` shows connected servers and lets you log in to ones that need it.

> ⚠️ Only add MCP servers you **trust**. A server can see the data you send it and can run actions for Claude.
