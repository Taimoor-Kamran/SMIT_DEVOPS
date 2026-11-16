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
