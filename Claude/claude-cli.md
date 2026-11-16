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
