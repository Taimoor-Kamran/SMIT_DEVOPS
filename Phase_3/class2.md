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

> **Beginner tip:** Most `systemctl` commands need `sudo` (admin permission)
> because services run at the system level, not as a regular user.

---

## Writing a Start/Stop Script

Instead of typing `systemctl start nginx` and `systemctl stop nginx` manually every time,
we write a script that accepts a command like `start`, `stop`, `restart`, or `status` as an argument.

### Version 1 — Simple Start/Stop Script

**File: service.sh**
```bash
#!/bin/bash

SERVICE="nginx"       # change this to any service name

if [ "$1" == "start" ]; then
    echo "Starting $SERVICE..."
    sudo systemctl start $SERVICE
    echo "$SERVICE started."

elif [ "$1" == "stop" ]; then
    echo "Stopping $SERVICE..."
    sudo systemctl stop $SERVICE
    echo "$SERVICE stopped."

else
    echo "Usage: ./service.sh [start|stop]"
    exit 1
fi
```

**How to use:**
```bash
chmod +x service.sh
./service.sh start     # starts nginx
./service.sh stop      # stops nginx
```

### Version 2 — Script with Status Check and restart

**File: service_v2.sh**
```bash
#!/bin/bash
set -e

SERVICE="nginx"

case "$1" in
    start)
        echo "Starting $SERVICE..."
        sudo systemctl start $SERVICE
        sudo systemctl status $SERVICE --no-pager
        ;;
    stop)
        echo "Stopping $SERVICE..."
        sudo systemctl stop $SERVICE
        echo "$SERVICE has been stopped."
        ;;
    restart)
        echo "Restarting $SERVICE..."
        sudo systemctl restart $SERVICE
        sudo systemctl status $SERVICE --no-pager
        ;;
    status)
        sudo systemctl status $SERVICE --no-pager
        ;;
    *)
        echo "Usage: ./service_v2.sh [start|stop|restart|status]"
        exit 1
        ;;
esac
```

> **What is `case`?**
> `case` is like `if/elif/else` but cleaner when you have many options.
> Each option ends with `;;` and the whole block ends with `esac` (case spelled backwards).

### Version 3 — Script that Accepts Service Name as Argument

Instead of hardcoding `SERVICE="nginx"`, pass the service name when running the script:

```bash
#!/bin/bash
set -e

SERVICE=$1    # first argument is the service name
ACTION=$2     # second argument is the action

if [ -z "$SERVICE" ] || [ -z "$ACTION" ]; then
    echo "Usage: ./service_v3.sh <service-name> [start|stop|restart|status]"
    echo "Example: ./service_v3.sh nginx start"
    exit 1
fi

case "$ACTION" in
    start)
        echo "Starting $SERVICE..."
        sudo systemctl start $SERVICE
        ;;
    stop)
        echo "Stopping $SERVICE..."
        sudo systemctl stop $SERVICE
        ;;
    restart)
        echo "Restarting $SERVICE..."
        sudo systemctl restart $SERVICE
        ;;
    status)
        sudo systemctl status $SERVICE --no-pager
        ;;
    *)
        echo "Unknown action: $ACTION"
        echo "Valid actions: start | stop | restart | status"
        exit 1
        ;;
esac

echo "Done. Exit code: $?"
```

**How to use:**
```bash
./service_v3.sh nginx start
./service_v3.sh mysql stop
./service_v3.sh docker restart
```

---

## What are Logs?

**Logs** are text files where a service writes a record of everything it does — every request, every error, every event — with a timestamp.

Think of logs as a **diary** that a service keeps for itself.
When something breaks, the log is the first place you look to understand what went wrong.

### Where are log files stored?

| Service | Log file location |
|---------|------------------|
| nginx | `/var/log/nginx/access.log` and `/var/log/nginx/error.log` |
| mysql | `/var/log/mysql/error.log` |
| ssh | `/var/log/auth.log` |
| systemd (all services) | `journalctl -u service-name` |
| your custom app | wherever your app writes — e.g. `./logs/app.log` |

### What a log entry looks like

```
2026-06-08 14:23:01 [INFO]  Server started on port 8080
2026-06-08 14:23:15 [INFO]  GET /home 200 OK - 12ms
2026-06-08 14:24:02 [ERROR] Database connection failed: timeout after 30s
2026-06-08 14:24:05 [WARN]  Retrying database connection (attempt 2/3)
```

Each line has:
- **Timestamp** — when it happened
- **Level** — INFO (normal), WARN (possible problem), ERROR (something broke)
- **Message** — what actually happened

---

## Tailing Logs with tail -f

The `tail` command shows you the **last few lines** of a file.
The `-f` flag means **follow** — it keeps watching and prints new lines as they are added in real time.

This is how DevOps engineers monitor a running service live.

### Basic tail Commands

```bash
tail file.log               # show last 10 lines (default)
tail -n 50 file.log         # show last 50 lines
tail -f file.log            # follow: live stream new lines as they appear
tail -f -n 100 file.log     # start from last 100 lines, then follow live
```

### Tailing Real Service Logs

```bash
# nginx web server logs
sudo tail -f /var/log/nginx/access.log    # every HTTP request in real time
sudo tail -f /var/log/nginx/error.log     # errors only

# mysql database logs
sudo tail -f /var/log/mysql/error.log

# systemd journal logs for any service
sudo journalctl -u nginx -f               # -f means follow (same as tail -f)
sudo journalctl -u nginx -n 50            # show last 50 lines
sudo journalctl -u nginx --since "1 hour ago"
```

### Filtering Logs with grep

Combine `tail` with `grep` to only show lines containing a keyword:

```bash
# show only ERROR lines in real time
tail -f /var/log/nginx/error.log | grep "ERROR"

# show only lines that contain a specific IP address
tail -f /var/log/nginx/access.log | grep "192.168.1.10"

# show errors and warnings together
tail -f app.log | grep -E "ERROR|WARN"
```

> Press `Ctrl + C` to stop `tail -f` at any time.

---

## Complete Script: Start, Stop and Tail Logs

This is the full production-style script that combines everything:
- accepts service name and action as arguments
- starts, stops, restarts, or checks status
- has a `logs` action to tail the service log live
- validates all inputs
- uses exit codes correctly

**File: manage.sh**

```bash
#!/bin/bash
set -e

# ─── Variables ────────────────────────────────────────────────
SERVICE=$1
ACTION=$2
LOG_DIR="/var/log"

# ─── Help message ─────────────────────────────────────────────
usage() {
    echo "Usage: ./manage.sh <service> <action>"
    echo ""
    echo "Actions:"
    echo "  start    — start the service"
    echo "  stop     — stop the service"
    echo "  restart  — restart the service"
    echo "  status   — show current status"
    echo "  logs     — tail live logs"
    echo ""
    echo "Examples:"
    echo "  ./manage.sh nginx start"
    echo "  ./manage.sh nginx logs"
    exit 1
}
```
