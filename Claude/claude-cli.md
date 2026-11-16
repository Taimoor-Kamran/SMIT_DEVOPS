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
