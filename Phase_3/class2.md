# Class 2: Write Script to Start/Stop Service and Tail Logs

---

## Table of Contents
1. [What is a Service?](#what-is-a-service)
2. [Managing Services with systemctl](#managing-services-with-systemctl)
3. [Writing a Start/Stop Script](#writing-a-startstop-script)
4. [What are Logs?](#what-are-logs)
5. [Tailing Logs with tail -f](#tailing-logs-with-tail--f)
6. [Complete Script: Start, Stop and Tail Logs](#complete-script-start-stop-and-tail-logs)
7. [Quick Reference](#quick-reference)

---

## What is a Service?

A **service** is a program that runs in the background on your Linux server — silently doing its job without you having to keep a terminal open.

**Real-world examples of services:**

| Service Name | What it does |
|-------------|-------------|
| `nginx` | Web server — serves your website to visitors |
| `mysql` | Database server — stores and retrieves data |
| `ssh` | Lets you connect to the server remotely |
| `docker` | Runs containers |
| `cron` | Runs scheduled tasks automatically |

On modern Linux systems, services are managed by **systemd** — the system and service manager.
You talk to systemd using the `systemctl` command.

```
You (terminal)  →  systemctl command  →  systemd  →  controls the service
```

---

## Managing Services with systemctl

### Core systemctl Commands

| Command | What it does |
|---------|-------------|
| `systemctl start nginx` | Start the nginx service right now |
| `systemctl stop nginx` | Stop the nginx service right now |
| `systemctl restart nginx` | Stop then start again (apply new config) |
| `systemctl reload nginx` | Reload config without full restart |
| `systemctl status nginx` | Show if service is running or stopped |
| `systemctl enable nginx` | Auto-start service when server boots |
| `systemctl disable nginx` | Do NOT auto-start on boot |
| `systemctl is-active nginx` | Prints "active" or "inactive" |

### Reading systemctl status Output

```bash
sudo systemctl status nginx
```

Output looks like this:
```
● nginx.service - A high performance web server
     Loaded: loaded (/lib/systemd/system/nginx.service; enabled)
     Active: active (running) since Sun 2026-06-08 14:00:00 PKT
    Process: 1234 ExecStart=/usr/sbin/nginx
   Main PID: 1234 (nginx)
```

**What each line means:**
- `Loaded` — the service file exists and is recognised by systemd
- `Active: active (running)` — service is currently running ✅
- `Active: inactive (dead)` — service is stopped ❌
- `Active: failed` — service crashed ⚠️
- `Main PID` — the process ID of the running service
