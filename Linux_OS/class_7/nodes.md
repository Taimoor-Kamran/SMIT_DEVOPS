# Linux Networking Commands (Class Notes)

## 1. IP Commands

ip a / ip addr
System ke saare network interfaces aur unke IP addresses show karta hai.

lo → Internal loopback network (system khud se baat karta hai)
eth0 → Main network interface (internet connection)

Network Types:

BROADCAST → Sab devices ko message
MULTICAST → Selected group ko message

ISP (Internet Service Provider):

PTCL
StormFiber
LeoNet
Zong / Jazz

Public IP check:

curl ifconfig.me

Other IP Commands:

ip link   # Interfaces ON/OFF status
ip route  # Routing table (internet ka rasta)
2. ifconfig
ifconfig

Ye command bhi ip a jaisa hi hai (older tool).

IP Types:

Local IP
LAN IP
Public IP

## 3. ping

ping google.com

Check karta hai ke internet ya server reachable hai ya nahi.

## 4. traceroute

traceroute google.com

Data packet ka path show karta hai.

Har line = ek router (hop)
time = latency (ms)

## 5. netstat
netstat -tlun

Open ports aur active connections check karne ke liye.

-t → TCP
-u → UDP
-l → Listening ports
-n → Numeric format
6. ss (Modern alternative of netstat)
ss -tuln
-t → TCP
-u → UDP
-l → Listening ports
-n → Numeric format
7. hostname
hostname
hostname -I
8. DNS Commands
nslookup
nslookup google.com

Sirf basic IP info deta hai.

dig
dig google.com

Detailed DNS information show karta hai.

DNS Records:

A → IPv4
AAAA → IPv6
MX → Mail server
CNAME → Alias
NS → Name servers
TXT → Text info (SPF, DKIM)

TTL (Time to Live): Kitni der tak DNS cache valid rahega.

9. curl
curl google.com

Website ka data fetch karta hai (API testing ke liye useful).

10. wget

File download karne ke liye use hota hai.

11. scp (Secure Copy)
scp file.txt user@ip:/home/

Ek system se dusre system me file copy karta hai.

12. SSH Setup (Local Server)
sudo apt update
sudo apt install openssh-server -y


sudo systemctl status ssh
sudo systemctl start ssh
sudo systemctl enable ssh


hostname -I

Connect:

ssh user@ip

SSH ek secure tarika hai remote login ka.

13. route
route -n

Routing table show karta hai.

Important Fields:

Destination → Network target
Gateway → Router
Genmask → Subnet mask
Iface → Interface name

Common Values:

0.0.0.0 → Default route
172.x.x.x → Local network
14. arp
arp -a

ARP table show karta hai (IP ↔ MAC mapping).

15. mtr (My Traceroute)

Install:

sudo apt update
sudo apt install mtr-tiny

Run:

mtr google.com

Fields:

Host → Routers
Loss% → Packet loss
Snt → Sent packets
Last → Last latency
Avg → Average latency
Best → Best latency
Wrst → Worst latency
StDev → Variation