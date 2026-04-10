# Linux Networking Commands (Class Notes)

## 1. ip a

Displays all network interfaces on the system along with their IP addresses.

lo (loopback): Internal network interface (system communicating with itself)
eth0: Main network interface (used for internet connection)
Networking Terms
BROADCAST: Sends data to all devices in the network
MULTICAST: Sends data to a specific group of devices
ISP (Internet Service Providers)
PTCL
StormFiber
LeoNet
Zong / Jazz (Mobile Data)
Useful Command
curl ifconfig.me

Shows your public IP address.

Related Commands
ip addr → Shows IP details
ip link → Shows interface status (UP/DOWN)
ip route → Shows routing table (network paths)

## 2. ifconfig

Alternative to ip a (older command)

Types of IP
Local IP
LAN IP
Public IP

## 3. ping google.com

Checks whether a server or internet connection is reachable.

## 4. traceroute google.com

Shows the path a data packet takes to reach the destination.

Each line represents a router (hop)
time shows latency in milliseconds (ms)

## 5. netstat -tlun

Displays open ports and active connections.

Options:

-t → TCP connections
-u → UDP connections
-l → Listening ports only
-n → Numeric format (IP & port numbers)

6. ss -tuln

Modern alternative to netstat (faster and more detailed)

Options:

-t → TCP connections
-u → UDP connections
-l → Listening sockets
-n → Numeric format

## 7. hostname / hostname -I

hostname → Shows system name
hostname -I → Shows system IP address

## 8. nslookup google.com

Finds the IP address of a domain name.

## 9. dig google.com

Provides detailed DNS information.

DNS Records
TTL (Time To Live): Time (in seconds) DNS data is cached
A Record: IPv4 address
AAAA Record: IPv6 address
MX Record: Mail server (e.g., smtp.google.com)
CNAME: Alias of a domain
NS Record: Name servers responsible for DNS queries
TXT Record: Stores text data (SPF, DKIM, verification, etc.)
Difference: nslookup vs dig
nslookup → Basic output (mainly IP)
dig → Detailed DNS information

## 10. curl google.com

Fetches website data in the terminal. Used for API testing and HTTP request checking.

## 11. wget

Downloads files from the internet.

## 12. scp

Copies files between systems.

Example:

scp file.txt user@ip:/home/
13. SSH Setup (Local Server)
Install SSH Server
sudo apt update
sudo apt install openssh-server -y
Manage SSH Service
sudo systemctl status ssh
sudo systemctl start ssh
sudo systemctl enable ssh
Get IP Address
hostname -I
Connect to Server
ssh user@ip

SSH (Secure Shell): A secure method to access and control a remote system via terminal.

14. route -n

Displays routing table.

Fields Explanation
Destination: Target network
0.0.0.0: Default route (all networks)
Gateway: Router used to reach external networks
Genmask: Subnet mask

Examples:

172.20.64.1 → Default gateway
255.255.240.0 → Subnet mask
Flags
U → Interface is up
G → Using a gateway
Iface
Network interface name (e.g., eth0)
15. arp -a

Displays ARP table (mapping of IP addresses to MAC addresses in local network).

16. mtr (My Traceroute)

Combination of ping and traceroute.

Installation
sudo apt update
sudo apt install mtr-tiny
Output Fields
Host: Network devices (routers/gateways)
Loss%: Packet loss
Snt: Sent packets
Last: Last response time
Avg: Average latency
Best: Best latency
Wrst: Worst latency
StDev: Latency variation

Useful for diagnosing slow networks and packet loss.