# DevOps Fundamentals & SSH Setup

## 1. DevOps Overview

DevOps is a modern software development approach that combines **Development (Dev)** and **Operations (Ops)** teams.  
Its goal is to improve collaboration, automate workflows, and deliver software faster with better quality.  
DevOps focuses on Continuous Integration, Continuous Delivery, monitoring, and fast feedback cycles.

---

## 2. What is SDLC in DevOps?

SDLC stands for **Software Development Life Cycle**.  
It is the complete process of planning, creating, testing, deploying, and maintaining software.  
In DevOps, SDLC becomes faster through automation and continuous delivery practices.

---

## 3. Phases of SDLC in DevOps

### Plan
In this phase, project requirements are collected and goals are defined.  
Teams decide features, timeline, tools, and resources for development.

### Code
Developers write source code using programming languages and version control tools like Git.  
Code is managed in repositories such as GitHub.

### Build
Source code is converted into executable applications or packages.  
Tools like Maven, Gradle, npm, or Docker are commonly used.

### Test
Application is tested to find bugs and ensure quality.  
Testing can be manual or automated.

### Release
After testing, a stable version is prepared for deployment.  
Approval and version tagging are usually done here.

### Deploy
Application is moved to servers or cloud environments for users.  
Deployment can be manual or automated.

### Operate
Operations team ensures the application runs smoothly in production.  
They manage servers, backups, scaling, and uptime.

### Monitor
Application and servers are monitored for errors, performance, and security.  
Tools like Grafana, Prometheus, and CloudWatch are used.

---

## 4. Environments in DevOps

### DEV (Development)
Used by developers to build and test features.

### SIT (System Integration Testing)
Used to test integration between modules and services.

### UAT (User Acceptance Testing)
Used by clients or testers to verify business requirements.

### PROD (Production)
Live environment where real users use the application.

---

## 5. What is SSH?

SSH stands for **Secure Shell**.  
It is a secure protocol used to connect remote servers through command line.  
SSH encrypts communication between client and server.

---

## 6. SSH Keys

SSH keys are more secure than passwords.  
They come in two parts:

- **Private Key** → Keep secret on your system  
- **Public Key** → Copy to server

SSH uses these keys for secure login without password.

---

# Practical Setup

## 7. Create SSH Keys in Linux / Ubuntu

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

Press Enter for default location:

```bash
~/.ssh/id_rsa
```

Files created:

```bash
~/.ssh/id_rsa       # Private key
~/.ssh/id_rsa.pub   # Public key
```

8. Connect to Fake Ubuntu Server (Your Own System)

If using WSL or another Ubuntu machine:

## Update Ubuntu

```bash
sudo apt update && sudo apt upgrade -y
```

Step 5: Install SSH Server

```bash
sudo apt install openssh-server -y
```

Start SSH:

```bash
sudo service ssh start
```

Check status:

```bash
sudo service ssh status
```

## Get Ubuntu IP Address

```bash
hostname -I
```

Example:

```bash
172.20.69.211
```

## Connect to Fake Server via SSH

From same PC (PowerShell / CMD / another terminal):

```bash
ssh taimoor@172.20.69.211
```

Now your Ubuntu acts like a real remote server.

Check IP:

```bash
hostname -I
```

## Connect to AWS EC2 Instance

### How to Create EC2 Instance in AWS & Connect Using SSH from Ubuntu

- Amazon EC2 is a cloud virtual server provided by AWS.
- You can create Ubuntu servers in the cloud and connect securely using SSH.

## Create EC2 Instance in AWS

### Login to AWS

- Open AWS Console and go to:

- EC2 Dashboard

Step 2: Launch Instance

Click:

Launch Instance

Step 3: Configure Instance

Fill details:

Name: Ubuntu-Server
AMI: Ubuntu Server 24.04 LTS
Instance Type: t2.micro (Free Tier eligible)
Key Pair: Create new key pair
Security Group: Allow SSH (Port 22)
Step 4: Create Key Pair

Choose:

Name: awskey
Type: RSA
Format: .pem

Download key file:

awskey.pem

Keep this file safe.

Step 5: Launch Instance

Click:

Launch Instance

After 1–2 minutes instance becomes running.

Part 2: Get Public IP

In EC2 dashboard copy:

Public IPv4 address

Example:

3.92.xx.xx
Part 3: Connect Using SSH from Ubuntu

Move .pem file into Ubuntu.

Step 1: Give Permission to Key
chmod 400 awskey.pem
Step 2: Connect to Server
ssh -i awskey.pem ubuntu@3.92.xx.xx

Example:

ssh -i awskey.pem ubuntu@54.210.xx.xx
Why Username is ubuntu?

Because Ubuntu AMI default user is:

ubuntu

Other images:

Amazon Linux → ec2-user
CentOS → centos
Debian → admin

Move .pem key file to Ubuntu system.

Give permission:

```bash
chmod 400 mykey.pem
```

Connect:

```bash
ssh -i mykey.pem ubuntu@public-ip
```

Example:

```bash
ssh -i awskey.pem ubuntu@3.92.xx.xx
```

10. Connect to Oracle VirtualBox Ubuntu VM

- First install Ubuntu VM in VirtualBox.
- Enable network adapter (Bridged or NAT).

Check VM IP:

```bash
hostname -I
```

Connect from host machine:

```bash
ssh username@vm-ip
```