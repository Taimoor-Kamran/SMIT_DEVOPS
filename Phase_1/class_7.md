# Class 7: Logs, Disk, Networking & Cloud Networking Intro

---

## Part 1 — Logs: /var/log, journald, and Log Rotation

---

### 1.1 What Are Logs and Why Do They Matter?

A **log** is a timestamped record of everything that happens on your system — service starts, errors, logins, crashes, warnings.

Without logs:
- You cannot know why a service crashed
- You cannot investigate a security breach
- You cannot prove what happened at 3 AM last Tuesday

```
Service crashes → you check the log → you see the error → you fix it
```

### 1.2 /var/log — The Log Directory

All system and service logs live under `/var/log`.

```bash
ls /var/log
```

Output (common files you will always see):

```
auth.log        → login attempts, sudo usage, SSH access
syslog          → general system messages
kern.log        → kernel messages (hardware, drivers)
dpkg.log        → package install/remove history
apt/            → apt update and install logs
nginx/          → nginx access and error logs
mysql/          → MySQL errors
journal/        → systemd journal binary logs
```

### Reading Logs

```bash
# Read the last 50 lines of syslog
tail -50 /var/log/syslog

# Follow syslog live — new lines appear as they arrive (Ctrl+C to stop)
tail -f /var/log/syslog

# Read the last 100 lines of auth.log
tail -100 /var/log/auth.log

# Search for errors in syslog
grep -i "error" /var/log/syslog

# Search for a specific service
grep "nginx" /var/log/syslog

# Combine — errors from nginx only
grep "nginx" /var/log/syslog | grep -i "error"
```

### Reading Rotated Logs (Compressed Old Logs)

Old log files are compressed to save space. They end in `.gz`.

```bash
# List all syslog files including old rotated ones
ls -lh /var/log/syslog*
```

Output:

```
-rw-r----- 1 syslog adm  1.2M May 10 syslog
-rw-r----- 1 syslog adm  980K May  9 syslog.1
-rw-r----- 1 syslog adm  120K May  8 syslog.2.gz
-rw-r----- 1 syslog adm   98K May  7 syslog.3.gz
```

```bash
# Read a compressed log without extracting it
zcat /var/log/syslog.2.gz | tail -50

# Search inside a compressed log
zgrep "error" /var/log/syslog.2.gz
```

---

### 1.3 journald — systemd's Log System

Modern Linux systems use **journald** alongside `/var/log`.  
journald collects logs from all systemd services and stores them in a structured binary format.

**The command to read journald logs is `journalctl`.**

### Basic journalctl Commands

```bash
# See all logs (oldest first) — press q to quit
journalctl

# See newest logs first
journalctl -r

# Follow live — like tail -f but for all services
journalctl -f

# Last 50 lines
journalctl -n 50

# Logs from a specific service
journalctl -u nginx
journalctl -u mysql
journalctl -u ssh

# Follow a specific service live
journalctl -u nginx -f

# Logs since a specific time
journalctl --since "2025-05-10 02:00:00"
journalctl --since "1 hour ago"
journalctl --since "yesterday"

# Logs between two times
journalctl --since "2025-05-10 01:00" --until "2025-05-10 03:00"

# Show only errors and above (critical, alert, emergency)
journalctl -p err

# Priority levels
journalctl -p 0   # emerg
journalctl -p 1   # alert
journalctl -p 2   # crit
journalctl -p 3   # err
journalctl -p 4   # warning
journalctl -p 5   # notice
journalctl -p 6   # info
journalctl -p 7   # debug
```

### Filtering journalctl Output

```bash
# Show logs for the last boot only
journalctl -b

# Show logs from the previous boot (last time you rebooted)
journalctl -b -1

# Show kernel messages only (equivalent to dmesg)
journalctl -k

# Disk usage of the journal
journalctl --disk-usage
```

### journalctl vs /var/log

| Feature | journalctl | /var/log files |
|---------|-----------|----------------|
| Format | Binary (structured) | Plain text |
| Filtering | Built-in by time, unit, priority | Use grep manually |
| Covers | All systemd services | Varies by service config |
| Searching by time | Native (`--since`) | Parse manually |
| View compressed old logs | Automatic | Need `zcat`/`zgrep` |
| Human-readable output | Yes — journalctl formats it | Raw text |

---

### 1.4 Log Rotation — Keeping Logs from Filling Your Disk

If logs are never deleted, they will eventually fill your disk and crash the system.  
**Log rotation** automatically compresses old logs, deletes very old ones, and keeps disk usage under control.

### logrotate — The Standard Tool

```bash
# The main config file
cat /etc/logrotate.conf

# Per-service rotation configs live here
ls /etc/logrotate.d/
```

Output:

```
apache2   apt   dpkg   mysql   nginx   rsyslog   ufw
```

### Reading a Rotation Config

```bash
cat /etc/logrotate.d/nginx
```

Output:

```
/var/log/nginx/*.log {
    daily               → rotate every day
    missingok           → don't error if log file doesn't exist
    rotate 14           → keep 14 old copies before deleting
    compress            → gzip old log files
    delaycompress       → compress the previous rotation, not the current
    notifempty          → skip rotation if log is empty
    sharedscripts
    postrotate          → run this command after rotating
        nginx -s reopen
    endscript
}
```

### Key logrotate Directives

| Directive | Meaning |
|-----------|---------|
| `daily` / `weekly` / `monthly` | How often to rotate |
| `rotate 14` | Keep 14 old copies |
| `compress` | gzip old logs |
| `maxsize 100M` | Rotate if log exceeds 100 MB even mid-cycle |
| `missingok` | No error if log file is absent |
| `notifempty` | Skip if log file is empty |
| `postrotate` | Shell commands to run after rotation (e.g., reload service) |

### Test a Rotation Config (Dry Run)

```bash
# Simulate rotation without actually doing it
sudo logrotate --debug /etc/logrotate.d/nginx
```

### Force Rotation Now (Useful in Disk Full Incidents)

```bash
sudo logrotate --force /etc/logrotate.conf
```

### journald Log Size Limits

```bash
# Check how much space journald is using
journalctl --disk-usage

# Vacuum old entries — keep only the last 7 days
sudo journalctl --vacuum-time=7d

# Vacuum old entries — keep only 500 MB
sudo journalctl --vacuum-size=500M
```

---

## Part 2 — Disk: df, du, and Inode Awareness

---

### 2.1 df — Disk Free (Filesystem-Level View)

`df` shows how full each mounted filesystem is.

```bash
# Human-readable sizes
df -h
```

Output:

```
Filesystem      Size  Used Avail Use%  Mounted on
/dev/sda1        50G   42G  4.8G  90%  /
/dev/sda2       100G   20G   80G  20%  /data
tmpfs           1.9G  1.1M  1.9G   1%  /run
```

**Columns explained:**

| Column | Meaning |
|--------|---------|
| `Filesystem` | The block device or volume |
| `Size` | Total capacity |
| `Used` | Space currently in use |
| `Avail` | Space available for new data |
| `Use%` | Percentage used — watch for values near 100% |
| `Mounted on` | Where it is attached in the directory tree |

```bash
# Show filesystem types as well
df -hT

# Show a specific directory's filesystem
df -h /var/log
```

**Warning thresholds:**

```
Use% < 70%   → Healthy
Use% 70-85%  → Monitor — growth is acceptable
Use% 85-95%  → Investigate — find and clean large files
Use% > 95%   → Urgent — services may start failing
Use% = 100%  → Emergency — services are already failing
```

---

### 2.2 du — Disk Usage (Directory-Level View)

`df` tells you a filesystem is 90% full. `du` tells you *what* is taking the space.

```bash
# Show size of a specific directory and everything inside it
du -sh /var/log
```

Output:

```
4.2G    /var/log
```

```bash
# Show sizes of all subdirectories inside /var/log
du -sh /var/log/*
```

Output:

```
12K     /var/log/apt
3.9G    /var/log/nginx
80M     /var/log/mysql
4.0K    /var/log/auth.log
```

**Drill down to find the big files:**

```bash
# Step 1: check overall disk usage
df -h /

# Step 2: find which top-level directory is biggest
du -sh /* 2>/dev/null | sort -rh | head -10

# Step 3: drill into that directory
du -sh /var/* 2>/dev/null | sort -rh | head -10

# Step 4: drill deeper
du -sh /var/log/* 2>/dev/null | sort -rh | head -10

# Step 5: find the specific large files
find /var/log/nginx -type f -size +100M
```

### Most Useful du Commands

```bash
# Size of current directory total
du -sh .

# Top 10 largest items in /var — sorted biggest first
du -sh /var/* 2>/dev/null | sort -rh | head -10

# Find all files larger than 500MB on the whole system
find / -type f -size +500M 2>/dev/null

# Find files larger than 1GB
find / -type f -size +1G 2>/dev/null -exec ls -lh {} \;
```

---

### 2.3 Inodes — The Hidden Disk Full Problem

**What is an inode?**

Every file on Linux has two parts:
1. The **data** — the actual content of the file
2. The **inode** — metadata about the file (name, permissions, timestamps, pointer to data)

Each filesystem has a fixed number of inodes created when it was formatted.  
If you run out of inodes, you cannot create new files — **even if there is free disk space**.

This is one of the most confusing "disk full" scenarios admins face.

### Check Inode Usage

```bash
# Show inode usage alongside disk usage
df -ih
```

Output:

```
Filesystem     Inodes  IUsed  IFree  IUse%  Mounted on
/dev/sda1        3.2M  3.2M      0   100%  /
/dev/sda2        6.4M  120K   6.3M     2%  /data
```

**First column is disk space (df -h), second is inode count (df -ih).**  
Here `/dev/sda1` has 0 free inodes — this is a disk-full-type emergency even if space exists.

### What Causes Inode Exhaustion?

```
Millions of tiny files:
  - Log systems that create one file per event
  - Mail spools with thousands of queued messages
  - PHP session files never cleaned up (/tmp/sess_*)
  - npm or pip caches storing thousands of small packages
```

### Find What Is Using All the Inodes

```bash
# Count files in each directory — sort by count
for dir in /*; do echo "$dir: $(find "$dir" -maxdepth 4 -type f 2>/dev/null | wc -l)"; done | sort -t: -k2 -rn | head -10

# Find directory with the most files
find / -xdev -printf '%h\n' 2>/dev/null | sort | uniq -c | sort -rn | head -20
```

### Fix Inode Exhaustion

```bash
# Example: PHP session file buildup
ls /var/lib/php/sessions/ | wc -l
# Output: 2847293    ← nearly 3 million files!

# Clean old session files (older than 1 day)
find /var/lib/php/sessions/ -type f -mtime +1 -delete

# Verify inodes freed
df -ih /
```

---

## Part 3 — Networking Basics: IP, DNS, HTTP

---

### 3.1 IP Addresses

Every device on a network has an **IP address** — a unique number that identifies it.

```
IPv4 address:  192.168.1.100
               ↑         ↑
            network    host

IPv4 format:   four numbers, each 0-255, separated by dots
IPv6 format:   2001:0db8:85a3:0000:0000:8a2e:0370:7334  (longer, newer)
```

### Check Your IP Address

```bash
# Show all network interfaces and their IPs
ip addr show

# Shorter alias
ip a
```

Output:

```
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP>
    inet 192.168.1.100/24 brd 192.168.1.255 scope global eth0
         ↑                ↑
       your IP          subnet mask (24 = 255.255.255.0)
```

```bash
# Show just your IPv4 addresses cleanly
hostname -I

# Show routing table — where traffic goes
ip route show
```

Output of `ip route show`:

```
default via 192.168.1.1 dev eth0      ← default gateway (your router)
192.168.1.0/24 dev eth0               ← local network
```

### IP Address Types

| Type | Range | Used For |
|------|-------|---------|
| Private (RFC 1918) | 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 | Internal networks — not routable on the internet |
| Public | Everything else | Internet-facing servers |
| Loopback | 127.0.0.1 | "This machine itself" — used for local testing |
| APIPA | 169.254.x.x | Auto-assigned when DHCP fails — sign of a problem |

### Subnet Masks and CIDR Notation

```
192.168.1.100/24

/24 means the first 24 bits are the network, last 8 are the host
/24  →  255.255.255.0  →  256 addresses (254 usable)
/16  →  255.255.0.0    →  65536 addresses
/8   →  255.0.0.0      →  16 million addresses
/32  →  255.255.255.255 → single host (1 address)
```

---

### 3.2 DNS — Domain Name System

DNS translates human-readable names into IP addresses.

```
You type:     google.com
DNS returns:  142.250.80.46
Browser uses: 142.250.80.46 to connect
```

### How DNS Resolution Works

```
1. You type: google.com
2. OS checks /etc/hosts (local override file)
3. OS asks your DNS resolver (set in /etc/resolv.conf)
4. Resolver asks root nameservers → TLD servers → google.com nameserver
5. Returns: 142.250.80.46
6. Your browser connects to that IP
```

### DNS Commands

```bash
# Look up an IP for a domain
nslookup google.com

# More detailed DNS lookup
dig google.com

# Lookup just the A record (IPv4 address)
dig google.com A

# Lookup MX records (mail servers)
dig google.com MX

# Lookup NS records (nameservers for a domain)
dig google.com NS

# Reverse lookup — IP to hostname
dig -x 8.8.8.8

# Check which DNS server your system uses
cat /etc/resolv.conf
```

Output of `dig google.com`:

```
;; ANSWER SECTION:
google.com.     300    IN    A    142.250.80.46

;; Query time: 12 msec
;; SERVER: 8.8.8.8#53(8.8.8.8)
```

### /etc/hosts — Local DNS Override

```bash
cat /etc/hosts
```

Output:

```
127.0.0.1   localhost
127.0.1.1   myserver
10.0.0.5    db-server db-server.internal
```

Entries in `/etc/hosts` override DNS — useful for:
- Testing before DNS records are updated
- Pointing a domain to a local development server
- Blocking domains by pointing them to 127.0.0.1

---

### 3.3 HTTP — How Web Communication Works

**HTTP (HyperText Transfer Protocol)** is how browsers and servers talk.

```
Client (browser) → sends HTTP Request  → Server
Client           ← receives HTTP Response ← Server
```

### HTTP Request Structure

```
GET /index.html HTTP/1.1
Host: example.com
User-Agent: curl/7.81.0
Accept: */*
```

```
Method   → GET (fetch), POST (send data), PUT (update), DELETE (remove)
Path     → /index.html — what resource we want
Host     → which server (needed when one IP serves many domains)
```

### HTTP Response Structure

```
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 1234

<html>...</html>
```

### HTTP Status Codes

| Code | Meaning | What It Tells You |
|------|---------|------------------|
| 200 | OK | Success — all good |
| 201 | Created | Resource was created (POST success) |
| 301 | Moved Permanently | URL changed — redirect |
| 302 | Found | Temporary redirect |
| 400 | Bad Request | Client sent malformed request |
| 401 | Unauthorized | Authentication required |
| 403 | Forbidden | Authenticated but no permission |
| 404 | Not Found | Resource does not exist |
| 500 | Internal Server Error | Server crashed handling the request |
| 502 | Bad Gateway | Reverse proxy got a bad response upstream |
| 503 | Service Unavailable | Server overloaded or down |
| 504 | Gateway Timeout | Upstream server too slow |

---

### 3.4 curl — Testing HTTP from the Terminal

`curl` is the most important tool for testing web endpoints, APIs, and connectivity.

### Basic curl Commands

```bash
# Fetch a webpage
curl http://example.com

# Fetch with HTTPS (SSL/TLS)
curl https://example.com

# Show only the HTTP response headers
curl -I https://example.com

# Show both headers and body
curl -v https://example.com

# Follow redirects (301/302)
curl -L https://example.com

# Save output to a file
curl -o output.html https://example.com

# Silent mode — no progress bar (useful in scripts)
curl -s https://example.com
```

### curl for API Testing

```bash
# GET request
curl https://api.example.com/users

# POST request with JSON body
curl -X POST https://api.example.com/users \
  -H "Content-Type: application/json" \
  -d '{"name": "Alice", "email": "alice@example.com"}'

# PUT request
curl -X PUT https://api.example.com/users/1 \
  -H "Content-Type: application/json" \
  -d '{"name": "Alice Updated"}'

# DELETE request
curl -X DELETE https://api.example.com/users/1

# Send with an authentication token
curl -H "Authorization: Bearer mytoken123" https://api.example.com/data

# Show just the HTTP status code
curl -s -o /dev/null -w "%{http_code}" https://example.com
```

### curl Diagnostics — Measure Connection Timing

```bash
# Show detailed timing breakdown
curl -s -o /dev/null -w "
DNS lookup:       %{time_namelookup}s
TCP connect:      %{time_connect}s
TLS handshake:    %{time_appconnect}s
First byte:       %{time_starttransfer}s
Total:            %{time_total}s
HTTP code:        %{http_code}
" https://example.com
```

Output:

```
DNS lookup:       0.023s
TCP connect:      0.045s
TLS handshake:    0.089s
First byte:       0.234s
Total:            0.241s
HTTP code:        200
```

---

### 3.5 ping — Testing Basic Connectivity

`ping` sends ICMP echo packets to test if a host is reachable.

```bash
# Basic ping — Ctrl+C to stop
ping google.com

# Ping exactly 5 times then stop
ping -c 5 google.com

# Ping with a 1-second interval (default)
ping -i 1 google.com

# Ping to test DNS resolution
ping -c 3 google.com
```

Output:

```
PING google.com (142.250.80.46) 56(84) bytes of data.
64 bytes from 142.250.80.46: icmp_seq=1 ttl=55 time=14.2 ms
64 bytes from 142.250.80.46: icmp_seq=2 ttl=55 time=13.8 ms
64 bytes from 142.250.80.46: icmp_seq=3 ttl=55 time=14.1 ms

--- google.com ping statistics ---
3 packets transmitted, 3 received, 0% packet loss
rtt min/avg/max = 13.8/14.0/14.2 ms
```

**Reading ping output:**

| Field | Meaning |
|-------|---------|
| `icmp_seq` | Sequence number — should be consecutive |
| `ttl` | Time To Live — hops remaining |
| `time` | Round-trip latency in milliseconds |
| `packet loss` | 0% = healthy, any loss = connectivity issue |

### Connectivity Troubleshooting with ping

```bash
# Step 1: Can you reach your own machine?
ping -c 2 127.0.0.1

# Step 2: Can you reach your gateway (router)?
ping -c 2 $(ip route | grep default | awk '{print $3}')

# Step 3: Can you reach the internet by IP? (no DNS needed)
ping -c 2 8.8.8.8

# Step 4: Can DNS resolve? (tests DNS layer)
ping -c 2 google.com
```

If step 3 works but step 4 fails → DNS problem (not network problem).

### traceroute — See the Path to a Destination

```bash
# Show every router hop to reach google.com
traceroute google.com

# On some systems
tracepath google.com
```

---

## Part 4 — Intro to Cloud Networking: VNet and Subnets

---

### 4.1 Why Cloud Networking Is Different

On a local network, you plug cables in and everything is on one network.  
In the cloud, you define your entire network in software — no cables.

This is called **Software-Defined Networking (SDN)**.

### 4.2 Virtual Network (VNet / VPC)

A **Virtual Network** (called VNet in Azure, VPC in AWS) is your private, isolated network inside the cloud.

```
Your cloud account
└── Virtual Network: 10.0.0.0/16
    ├── Subnet A (Public):  10.0.1.0/24   → web servers facing the internet
    ├── Subnet B (Private): 10.0.2.0/24   → app servers, internal only
    └── Subnet C (Private): 10.0.3.0/24   → databases, most restricted
```

Think of a VNet as your own private data center network — but it lives entirely in software.

### 4.3 Subnets — Dividing Your Network

A **subnet** is a smaller range carved out of your VNet.  
Subnets let you separate resources and control who can talk to whom.

```
VNet:   10.0.0.0/16   → 65,536 addresses (your whole private cloud network)
├── Public Subnet:   10.0.1.0/24  → 256 addresses — internet-facing
├── App Subnet:      10.0.2.0/24  → 256 addresses — internal only
└── DB Subnet:       10.0.3.0/24  → 256 addresses — most restricted
```

**Why use multiple subnets?**

```
Public Subnet   → Load balancers and web servers
                  Has a route to the internet
                  Exposed to users

App Subnet      → Application servers (Node.js, Python, etc.)
                  Can talk to web servers and databases
                  NOT directly exposed to the internet

DB Subnet       → Database servers (MySQL, PostgreSQL)
                  Can ONLY talk to the app subnet
                  Never exposed to the internet directly
```

### 4.4 Internet Gateway and NAT Gateway

| Component | Purpose |
|-----------|---------|
| **Internet Gateway** | Allows resources in the public subnet to receive traffic from the internet |
| **NAT Gateway** | Allows resources in private subnets to initiate outbound internet connections (e.g., download packages) without being reachable from the internet |

```
Internet
    ↓
Internet Gateway
    ↓
Public Subnet (10.0.1.0/24)
    ↓
Load Balancer / Web Server

Private Subnet (10.0.2.0/24)
App Server → NAT Gateway → Internet (for outbound only)
DB Server  → NO internet access at all
```

### 4.5 Security Groups and Network ACLs

These are cloud firewalls — they control what traffic is allowed.

**Security Group (instance-level firewall):**

```
Inbound Rules:
  Allow TCP port 80   from 0.0.0.0/0        → anyone can reach port 80 (HTTP)
  Allow TCP port 443  from 0.0.0.0/0        → anyone can reach port 443 (HTTPS)
  Allow TCP port 22   from 10.0.0.0/8       → SSH only from internal network
  Deny  ALL           from 0.0.0.0/0        → block everything else

Outbound Rules:
  Allow ALL to 0.0.0.0/0                    → server can initiate any outbound connection
```

**Network ACL (subnet-level firewall):**

```
Applied to the entire subnet, not individual instances.
More coarse-grained than security groups.
Stateless — must define both inbound AND outbound rules.
```

### 4.6 DNS in the Cloud

Cloud providers give each VNet a built-in DNS resolver.

```
AWS:   169.254.169.253 (or .2 address in your VPC)
Azure: 168.63.129.16
GCP:   169.254.169.254

Private DNS zones let you resolve internal names:
  db-server.internal → 10.0.3.10  (only works inside your VNet)
  app-server.internal → 10.0.2.5
```

### 4.7 Cloud Networking Concept Summary

```
VNet/VPC
│
├── Public Subnet ──── Internet Gateway ──── Internet
│   └── Web Server (has public IP)
│
├── Private App Subnet ──── NAT Gateway ──→ Internet (outbound only)
│   └── App Server (private IP only)
│
└── Private DB Subnet (no internet route at all)
    └── Database Server (private IP only)

Security Groups: per-instance firewall rules
Network ACLs:    per-subnet firewall rules
Route Tables:    control where traffic goes from each subnet
```

---

## Part 5 — Lab: Analyze Logs, Check Disk Usage, Test Endpoints

---

### Lab Task 1: Analyze Logs for Errors

```bash
# Step 1: Check what log files exist
ls -lh /var/log/

# Step 2: Find all ERROR lines in syslog from the last hour
sudo grep -i "error" /var/log/syslog | tail -50

# Step 3: Check authentication log for failed SSH attempts
sudo grep "Failed password" /var/log/auth.log | tail -20

# Step 4: Count how many failed login attempts
sudo grep "Failed password" /var/log/auth.log | wc -l

# Step 5: Find which IPs are trying to brute-force SSH
sudo grep "Failed password" /var/log/auth.log | awk '{print $11}' | sort | uniq -c | sort -rn | head -10

# Step 6: Check nginx error log (if nginx is installed)
sudo tail -100 /var/log/nginx/error.log

# Step 7: Use journalctl to see only errors from the last 2 hours
sudo journalctl -p err --since "2 hours ago"

# Step 8: Check for Out-Of-Memory (OOM) kills — process killed due to no RAM
sudo journalctl -k | grep -i "out of memory"
sudo grep -i "out of memory" /var/log/syslog
```

### Lab Task 2: Check Disk Usage

```bash
# Step 1: Overall disk usage
df -h

# Step 2: Check inode usage
df -ih

# Step 3: Find the largest directories on the system
du -sh /* 2>/dev/null | sort -rh | head -10

# Step 4: Drill into /var (common culprit)
du -sh /var/* 2>/dev/null | sort -rh | head -10

# Step 5: Find the largest individual files
find / -type f -size +100M 2>/dev/null -exec ls -lh {} \; | sort -k5 -rh | head -10

# Step 6: Check log directory specifically
du -sh /var/log/*  2>/dev/null | sort -rh | head -10

# Step 7: Check for large core dump files
find / -name "core" -type f 2>/dev/null
find /tmp -type f -size +50M 2>/dev/null

# Step 8: Check journald disk usage
journalctl --disk-usage
```

### Lab Task 3: Test Endpoints with curl

```bash
# Step 1: Test if your web server responds
curl -I http://localhost

# Step 2: Test with full output
curl -v http://localhost 2>&1 | head -30

# Step 3: Check response time
curl -s -o /dev/null -w "HTTP Status: %{http_code}\nTime: %{time_total}s\n" http://localhost

# Step 4: Test HTTPS (skip cert check for self-signed)
curl -k -I https://localhost

# Step 5: Test an external API
curl -s https://httpbin.org/ip | python3 -m json.tool

# Step 6: Test DNS resolution
dig +short google.com
nslookup github.com

# Step 7: Test basic connectivity
ping -c 3 8.8.8.8
ping -c 3 google.com

# Step 8: Test if a port is open
nc -zv google.com 443
nc -zv localhost 80
```

---

## Part 6 — Incident: Disk Full Causing Service Failure

---

### Scenario

It is 03:30. Your monitoring fires:

```
ALERT: web-server-02
  - nginx is returning 502 Bad Gateway
  - MySQL cannot write to disk
  - Disk /dev/sda1: 100% full
```

Users are getting errors on the website. The disk is completely full.  
Your job: find what filled the disk, clean it safely, and restore the service.

---

### Step 1: Confirm the Problem

```bash
df -h
```

Output:

```
Filesystem      Size  Used  Avail  Use%  Mounted on
/dev/sda1        50G   50G      0  100%  /
/dev/sda2       100G   30G   70G   30%  /data
```

`/dev/sda1` is at 100%. Zero bytes available.

```bash
# Check the services
sudo systemctl status nginx
sudo systemctl status mysql
```

Output:

```
● nginx.service — A high performance web server
   Loaded: loaded
   Active: active (running)
● mysql.service — MySQL Community Server
   Loaded: loaded
   Active: failed (Result: exit-code)
```

MySQL has crashed. Nginx is running but getting 502 because MySQL is down.

```bash
# Check MySQL error log for the actual failure reason
sudo tail -20 /var/log/mysql/error.log
```

Output:

```
[ERROR] InnoDB: The innodb_system data file 'ibdata1' must be writable
[ERROR] InnoDB: Cannot initialize InnoDB as 'datadir' is full
[ERROR] Aborting
```

MySQL cannot write its data files — disk is full.

---

### Step 2: Find What Is Consuming the Disk

```bash
# Find the largest directories — start at root
du -sh /* 2>/dev/null | sort -rh | head -10
```

Output:

```
45G    /var
2.1G   /usr
1.2G   /opt
800M   /home
```

`/var` is using 45GB out of 50GB. Drill in.

```bash
du -sh /var/* 2>/dev/null | sort -rh | head -10
```

Output:

```
38G    /var/log
4.1G   /var/lib
1.2G   /var/cache
```

`/var/log` is 38GB — far too large. Drill deeper.

```bash
du -sh /var/log/* 2>/dev/null | sort -rh | head -10
```

Output:

```
37G    /var/log/nginx
450M   /var/log/mysql
200M   /var/log/syslog
```

`/var/log/nginx` alone is 37GB. Check what is in there.

```bash
ls -lh /var/log/nginx/
```

Output:

```
-rw-r--r-- 1 www-data adm   37G May 10 03:15 access.log
-rw-r--r-- 1 www-data adm   12M May 10 03:15 error.log
-rw-r--r-- 1 www-data adm   2.3G May  9 access.log.1
```

**Root cause:** `access.log` has grown to 37GB. The logrotate job was not running — possibly because it was misconfigured or skipped. The access log was never rotated or compressed.

---

### Step 3: Free Space Safely (Do NOT Delete Blindly)

**Important: never just `rm` log files that a running process has open. Truncate them instead.**

When nginx has a log file open and you delete it:
- The filename disappears from the directory
- But nginx still holds the file descriptor open
- The space is NOT freed until nginx releases the handle
- You think you deleted it but disk usage stays the same

**Safe way to clear a log file that a running process has open:**

```bash
# Step 1: Check if nginx has the file open
sudo lsof /var/log/nginx/access.log
```

Output:

```
COMMAND  PID      USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
nginx    2341  www-data   5w   REG  8,1   37G      ... /var/log/nginx/access.log
```

Nginx has it open. Truncate it — do not delete it.

```bash
# Truncate the file to 0 bytes (safe — nginx keeps its file handle)
sudo truncate -s 0 /var/log/nginx/access.log

# Verify space was freed
df -h /
```

Output:

```
Filesystem      Size  Used  Avail  Use%  Mounted on
/dev/sda1        50G   13G   37G   26%  /
```

37GB freed immediately.

---

### Step 4: Clean Up Additional Cruft

```bash
# Remove the old access.log.1 (already rotated — safe to delete)
sudo rm /var/log/nginx/access.log.1

# Compress and vacuum journald logs — keep only 7 days
sudo journalctl --vacuum-time=7d

# Clear apt cache
sudo apt clean

# Remove old package files
sudo apt autoremove -y

# Check disk again
df -h /
```

Output:

```
Filesystem      Size  Used  Avail  Use%  Mounted on
/dev/sda1        50G  8.2G   42G   17%  /
```

Down from 100% to 17%. Plenty of room.

---

### Step 5: Restore the Services

```bash
# Restart MySQL first — it needs disk to start
sudo systemctl restart mysql
sudo systemctl status mysql
```

Output:

```
● mysql.service — MySQL Community Server
   Active: active (running) since 03:42:11
```

MySQL is back.

```bash
# Reload nginx — picks up any config changes
sudo systemctl reload nginx
sudo systemctl status nginx
```

```bash
# Test the web server is responding
curl -I http://localhost
```

Output:

```
HTTP/1.1 200 OK
Server: nginx/1.24.0
```

Website is restored.

---

### Step 6: Prevent This From Happening Again

**Fix logrotate for nginx:**

```bash
# Check why logrotate was not running
sudo logrotate --debug /etc/logrotate.d/nginx

# Look at the logrotate config
cat /etc/logrotate.d/nginx
```

**Update the config to add size-based rotation:**

```bash
sudo nano /etc/logrotate.d/nginx
```

Updated config:

```
/var/log/nginx/*.log {
    daily
    missingok
    rotate 7
    compress
    delaycompress
    notifempty
    maxsize 500M        ← NEW: rotate if log exceeds 500MB even mid-day
    sharedscripts
    postrotate
        nginx -s reopen
    endscript
}
```

```bash
# Test the updated config
sudo logrotate --debug /etc/logrotate.d/nginx

# Force it to run now and verify it works
sudo logrotate --force /etc/logrotate.d/nginx

# Check the result
ls -lh /var/log/nginx/
```

**Add a disk usage alert (cron-based):**

```bash
sudo crontab -e
```

Add:

```bash
# Check disk every 15 minutes — alert if over 85%
*/15 * * * * df / | awk 'NR==2 {gsub("%",""); if ($5 > 85) print "DISK WARNING: " $5 "% used on " $6}' | logger -t disk-monitor
```

---

### Incident Summary

| Step | Action | Command |
|------|--------|---------|
| 1 | Confirmed disk at 100% | `df -h` |
| 2 | Confirmed MySQL crashed with disk-full error | `journalctl -u mysql` |
| 3 | Found /var was 45G | `du -sh /* \| sort -rh` |
| 4 | Found /var/log/nginx was 38G | `du -sh /var/log/*` |
| 5 | Found access.log was 37G | `ls -lh /var/log/nginx/` |
| 6 | Confirmed nginx had it open | `lsof /var/log/nginx/access.log` |
| 7 | Truncated safely (not deleted) | `truncate -s 0 access.log` |
| 8 | Cleared journal, apt cache | `journalctl --vacuum-time=7d` |
| 9 | Restarted MySQL | `systemctl restart mysql` |
| 10 | Reloaded nginx | `systemctl reload nginx` |
| 11 | Verified website restored | `curl -I http://localhost` |
| 12 | Fixed logrotate config | Added `maxsize 500M` |

### Safe Cleanup Rules

| Situation | Safe Action | Unsafe Action |
|-----------|------------|---------------|
| Log file open by running process | `truncate -s 0 file.log` | `rm file.log` (space not freed) |
| Old rotated logs (`.log.1`, `.log.2.gz`) | `rm` them — safe | — |
| Core dump files | `rm` them — safe | — |
| MySQL data files | Never touch manually | `rm` = data loss |
| `/tmp` files older than 7 days | `find /tmp -mtime +7 -delete` | Deleting active temp files |
| apt cache | `apt clean` | — |
| journald | `journalctl --vacuum-time=7d` | Deleting `/var/log/journal/` directly |

---

## Summary

### Quick Reference Table

| Topic | Key Command | What It Shows |
|-------|------------|---------------|
| View logs live | `tail -f /var/log/syslog` | New syslog entries as they arrive |
| Service logs | `journalctl -u nginx -f` | Live nginx logs via journald |
| Find errors | `journalctl -p err --since "1 hour ago"` | Errors in the last hour |
| Disk space | `df -h` | Filesystem usage |
| Inode usage | `df -ih` | Inode usage per filesystem |
| Find big dirs | `du -sh /* \| sort -rh \| head -10` | Largest directories |
| Find big files | `find / -size +500M` | Files over 500MB |
| Free log safely | `truncate -s 0 /var/log/app.log` | Zero a log without breaking the process |
| Rotate logs now | `logrotate --force /etc/logrotate.conf` | Force log rotation |
| Vacuum journald | `journalctl --vacuum-time=7d` | Delete old journal entries |
| Test connectivity | `ping -c 3 8.8.8.8` | Basic network reachability |
| Test DNS | `dig google.com` | DNS resolution |
| Test HTTP | `curl -I http://localhost` | HTTP response headers |
| Measure response | `curl -w "%{time_total}" -o /dev/null -s url` | Response time |

### Golden Rules

> 1. **Never delete log files that are open by a running process.** Use `truncate -s 0` to empty them safely.
> 2. **Disk full = check /var/log first.** Logs are the most common cause.
> 3. **Check both disk space AND inodes.** You can run out of inodes even with free space.
> 4. **SIGTERM before SIGKILL — and investigate before you clean.** Make sure you know what the file is before deleting.
> 5. **Fix the root cause.** After clearing the disk, fix logrotate so it never fills up again.
> 6. **Always test the service after recovery.** `systemctl status` and `curl` to confirm it actually works.
