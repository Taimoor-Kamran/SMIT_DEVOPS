# Jenkins: Installation and Setup on Ubuntu (Step by Step)

---

## Table of Contents
1. [What We Will Do](#what-we-will-do)
2. [Requirements](#requirements)
3. [Option A: Create an Ubuntu Server on AWS EC2](#option-a-create-an-ubuntu-server-on-aws-ec2)
4. [Option B: Use WSL or a Local Ubuntu VM](#option-b-use-wsl-or-a-local-ubuntu-vm)
5. [Step 1: Update Ubuntu](#step-1-update-ubuntu)
6. [Step 2: Install Java 21](#step-2-install-java-21)
7. [Step 3: Add the Jenkins Repository](#step-3-add-the-jenkins-repository)
8. [Step 4: Install Jenkins](#step-4-install-jenkins)
9. [Step 5: Start and Enable the Jenkins Service](#step-5-start-and-enable-the-jenkins-service)
10. [Step 6: Open Port 8080](#step-6-open-port-8080)
11. [Step 7: Unlock Jenkins in the Browser](#step-7-unlock-jenkins-in-the-browser)
12. [Step 8: Install Plugins](#step-8-install-plugins)
13. [Step 9: Create the First Admin User](#step-9-create-the-first-admin-user)
14. [Step 10: Set the Jenkins URL](#step-10-set-the-jenkins-url)
15. [Tour of the Jenkins Dashboard](#tour-of-the-jenkins-dashboard)
16. [Install Extra Plugins](#install-extra-plugins)
17. [Install Git and Docker for Jenkins](#install-git-and-docker-for-jenkins)
18. [Change the Jenkins Port](#change-the-jenkins-port)
19. [Important Files and Folders](#important-files-and-folders)
20. [Manage the Jenkins Service](#manage-the-jenkins-service)
21. [Update, Back Up and Uninstall](#update-back-up-and-uninstall)
22. [Troubleshooting](#troubleshooting)
23. [Quick Reference](#quick-reference)

---

## What We Will Do

```
 Ubuntu server
   │
   ├── 1. Install Java 21         (Jenkins is a Java program)
   ├── 2. Add Jenkins apt repo    (so apt knows where to download Jenkins)
   ├── 3. apt install jenkins
   ├── 4. Start the service       (systemctl)
   ├── 5. Open port 8080
   └── 6. Finish setup in browser → http://<server-ip>:8080
```

We will install the **LTS (Long-Term Support)** version — it's the stable version recommended for real use.

| Release line | Updates | Use for |
|--------------|---------|---------|
| **LTS** (stable) | Every ~12 weeks, well tested | ✅ Learning and production |
| **Weekly** | Every week, newest features | Trying new features |

---

## Requirements

| Item | Minimum | Recommended (small team) |
|------|---------|-------------------------|
| RAM | 256 MB | **4 GB+** |
| Disk | 1 GB | **50 GB+** |
| Java | **Java 21** or newer (Java 25 also supported) | OpenJDK 21 |
| OS | Ubuntu 22.04 / 24.04 LTS | Ubuntu 24.04 LTS |
| User | A user with `sudo` access | — |

> ⚠️ **Old Java versions (8, 11, 17) are NOT supported** by current Jenkins. Many old tutorials use Java 11 or 17 — they won't work now.
>
> In practice, Jenkins + a few builds needs **at least 2 GB RAM**. Builds with Docker need more.

---

## Option A: Create an Ubuntu Server on AWS EC2

If you want Jenkins on the cloud (like a real company), create an EC2 instance first. (You learned EC2 basics in `Phase_1/class1.md`.)

- **AWS Console → EC2 → Launch instance**
- **Name:** `jenkins-server`
- **AMI:** Ubuntu Server 24.04 LTS
- **Instance type:** `t3.small` (2 GB RAM) or `t3.medium` (4 GB RAM)

> ⚠️ `t2.micro` / `t3.micro` (1 GB, Free Tier) is **too small** — Jenkins becomes very slow or crashes.
> `t3.small` costs a little money, so **stop the instance** after practice.

- **Key pair:** create or choose one (e.g. `jenkins-key.pem`)
- **Network settings → Edit → Security group rules:**

| Type | Port | Source | Why |
|------|------|--------|-----|
| SSH | 22 | **My IP** | Connect to the server |
| Custom TCP | **8080** | **My IP** | Open the Jenkins web page |

- **Storage:** 20 GB or more
- Click **Launch instance**

**Connect:**

```bash
chmod 400 jenkins-key.pem
ssh -i jenkins-key.pem ubuntu@<EC2-PUBLIC-IP>
```

> Using **My IP** instead of `0.0.0.0/0` (anywhere) means only **you** can reach Jenkins. Never leave Jenkins open to the whole internet.

---

## Option B: Use WSL or a Local Ubuntu VM

You can also install Jenkins on:
- **WSL Ubuntu** (`Linux_OS/class_1`) — open `http://localhost:8080` in your Windows browser
- A **VirtualBox Ubuntu VM** (`Phase_1/class1.md`) — open `http://<VM-IP>:8080`

> On WSL, make sure **systemd** is enabled (it is by default in new WSL Ubuntu). Check with `systemctl --version`.

---

## Step 1: Update Ubuntu

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y wget
```

---

## Step 2: Install Java 21

Jenkins is written in Java, so Java must be installed **before** Jenkins.

```bash
sudo apt update
sudo apt install -y fontconfig openjdk-21-jre
```

Check it:

```bash
java -version
```

Output (similar):

```
openjdk version "21.0.x" 2025-xx-xx
OpenJDK Runtime Environment (build 21.0.x+x-Ubuntu)
OpenJDK 64-Bit Server VM (build 21.0.x+x-Ubuntu, mixed mode, sharing)
```

> **What is `fontconfig`?** Jenkins draws some graphs and images; `fontconfig` gives it the fonts to do that.
> **JRE vs JDK:** the **JRE** (Java Runtime) is enough to **run** Jenkins. Install the **JDK** (`openjdk-21-jdk`) only if your builds compile Java code.
