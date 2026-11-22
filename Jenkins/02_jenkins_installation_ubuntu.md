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

---

## Step 3: Add the Jenkins Repository

Ubuntu's normal software list doesn't include Jenkins. We add Jenkins' own **apt repository**.

### 3.1 Download the Jenkins signing key

```bash
sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2026.key
```

### 3.2 Add the repository

```bash
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
```

**What these commands mean:**

| Part | Meaning |
|------|---------|
| `jenkins.io-2026.key` | Jenkins' **signing key** — proves packages really come from Jenkins, not a hacker |
| `/etc/apt/keyrings/` | Folder where apt stores trusted keys |
| `signed-by=...` | Only trust packages from this repo if they're signed by **this** key |
| `debian-stable` | The **LTS** repository (use `debian` for weekly) |
| `/etc/apt/sources.list.d/jenkins.list` | File that tells apt where to download Jenkins from |
| `tee ... > /dev/null` | Write the line into the file with sudo, hide the screen output |

> ⚠️ The key file name has a **year** in it (`2026`). Jenkins changes the key from time to time.
> If `apt update` shows a **`NO_PUBKEY`** or **signature** error, check the latest key name on
> `https://www.jenkins.io/doc/book/installing/linux/` and run Step 3.1 again.

---

## Step 4: Install Jenkins

```bash
sudo apt update
sudo apt install -y jenkins
```

During install, the package automatically:
- Creates a Linux user called **`jenkins`** (see `03_jenkins_users.md`)
- Creates the Jenkins home folder **`/var/lib/jenkins`**
- Creates a **systemd service** called `jenkins`

Check the version:

```bash
jenkins --version
```

---

## Step 5: Start and Enable the Jenkins Service

```bash
sudo systemctl enable jenkins     # start automatically when the server boots
sudo systemctl start jenkins      # start it now
sudo systemctl status jenkins     # check it is running
```

Look for **`active (running)`** in green:

```
● jenkins.service - Jenkins Continuous Integration Server
     Loaded: loaded (/usr/lib/systemd/system/jenkins.service; enabled)
     Active: active (running) since ...
```

Press `q` to leave the status screen.

> You learned `systemctl` in `Phase_3/class2.md`. Jenkins is just another **service**, like nginx.

Check that Jenkins is listening on port 8080:

```bash
sudo ss -tlnp | grep 8080
```

---

## Step 6: Open Port 8080

### On AWS EC2
You already allowed port **8080** in the **security group** (Option A). Nothing else to do.

### If Ubuntu's firewall (UFW) is on

```bash
sudo ufw status
```

If it says `Status: active`:

```bash
sudo ufw allow OpenSSH       # don't lock yourself out!
sudo ufw allow 8080/tcp
sudo ufw status
```

> If UFW shows `inactive`, you don't need to do anything.

---

## Step 7: Unlock Jenkins in the Browser

### 7.1 Open Jenkins

In your browser go to:

| Where Jenkins is installed | URL |
|---------------------------|-----|
| AWS EC2 | `http://<EC2-PUBLIC-IP>:8080` |
| WSL | `http://localhost:8080` |
| VirtualBox VM | `http://<VM-IP>:8080` (find IP with `hostname -I`) |

You will see the **Unlock Jenkins** page. It asks for an **Administrator password**.

### 7.2 Get the password

On the server:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Output (example):

```
3f2a9c7d1e8b4a6f9c0d2e5b7a1c3d4e
```

### 7.3 Paste it

- Copy the password → paste it in the **Administrator password** box → click **Continue**

> This one-time password makes sure **only the person with server access** can set up Jenkins.

---

## Step 8: Install Plugins

The **Customize Jenkins** page gives two choices:

| Option | What it does |
|--------|-------------|
| **Install suggested plugins** | Installs the most common plugins (Git, Pipeline, Credentials, GitHub Branch Source, Timestamper, Matrix Authorization...) |
| **Select plugins to install** | You pick each plugin yourself |

- Click **Install suggested plugins** ✅ (best for beginners)

Wait a few minutes while each plugin shows a green tick ✅.

> If some plugins fail (red ❌), click **Retry**. It's usually a slow internet connection.

---

## Step 9: Create the First Admin User

Fill in the **Create First Admin User** form:

| Field | Example |
|-------|---------|
| Username | `admin` (or your name, e.g. `taimoor`) |
| Password | a **strong** password |
| Confirm password | same password |
| Full name | `Taimoor Kamran` |
| E-mail address | your email |

- Click **Save and Continue**

> ⚠️ Save this password in a password manager. This user can do **everything** in Jenkins.
> From now on, the `initialAdminPassword` no longer works.

---

## Step 10: Set the Jenkins URL

The **Instance Configuration** page shows **Jenkins URL**, e.g. `http://3.110.25.40:8080/`.

- Keep it as it is (it's the address you used in the browser)
- Click **Save and Finish** → **Start using Jenkins** 🎉

> On EC2, the **public IP changes** every time you stop and start the instance.
> Update it later in **Manage Jenkins → System → Jenkins URL**, or attach an **Elastic IP** to keep it fixed.

---

## Tour of the Jenkins Dashboard

```
 ┌───────────────────────────────────────────────────────────────┐
 │ Jenkins                                    🔍  admin ▾  Log out│
 ├──────────────────┬────────────────────────────────────────────┤
 │ + New Item       │                                            │
 │ Build History    │   Welcome to Jenkins!                      │
 │ Manage Jenkins   │   (your jobs will be listed here)          │
 │ My Views         │                                            │
 │                  │                                            │
 │ Build Queue      │                                            │
 │ Build Executor   │                                            │
 │  Status          │                                            │
 └──────────────────┴────────────────────────────────────────────┘
```

| Menu | What it's for |
|------|---------------|
| **New Item** | Create a new job / pipeline |
| **Build History** | All builds of all jobs |
| **Manage Jenkins** | All settings (plugins, users, security, credentials, nodes) |
| **Build Queue** | Builds waiting to run |
| **Build Executor Status** | Which machines are running builds right now |

### Inside Manage Jenkins

| Section | What you do there |
|---------|-------------------|
| **System** | Jenkins URL, number of executors, global environment variables |
| **Tools** | Configure Git, JDK, Maven, Node.js installations |
| **Plugins** | Install, update and remove plugins |
| **Nodes** | Add agent machines |
| **Credentials** | Store passwords, tokens and SSH keys |
| **Security** | Login and permission settings |
| **Users** | Create and manage Jenkins users |
| **System Log** | Jenkins' own error logs |

---

## Install Extra Plugins

**Example:** install the **Docker Pipeline** plugin.

- **Manage Jenkins → Plugins**
- Click **Available plugins** (left side)
- Search: `Docker Pipeline`
- Tick ✅ it → click **Install**
- Tick **Restart Jenkins when installation is complete and no jobs are running** (if shown)

| Tab | Shows |
|-----|-------|
| **Updates** | Plugins with a newer version — update them regularly |
| **Available plugins** | Plugins you can install |
| **Installed plugins** | What you have — you can disable or uninstall here |
| **Advanced settings** | Upload a plugin file manually, proxy settings |

**Restart Jenkins safely** (waits for running builds to finish): open `http://<your-jenkins>:8080/safeRestart`

---

## Install Git and Docker for Jenkins

Pipelines run commands **on the server** as the `jenkins` user, so the tools must be installed there.

```bash
# Git
sudo apt install -y git
git --version

# Docker
sudo apt install -y docker.io
sudo systemctl enable --now docker

# Allow the jenkins user to run docker commands
sudo usermod -aG docker jenkins
sudo systemctl restart jenkins
```

Check it works as the `jenkins` user:

```bash
sudo -u jenkins docker ps
```

If you see an empty table (not "permission denied"), it works. ✅

> ⚠️ Being in the `docker` group is almost the same as having **root** access.
> It's fine for a learning server — read `03_jenkins_users.md` for why.

---

## Change the Jenkins Port

If port **8080** is already used by another app:

```bash
sudo systemctl edit jenkins
```

An editor opens. Add these lines in the empty area at the top:

```ini
[Service]
Environment="JENKINS_PORT=8081"
```

Save and exit, then:

```bash
sudo systemctl daemon-reload
sudo systemctl restart jenkins
sudo ss -tlnp | grep 8081
```

Now open `http://<server-ip>:8081`. Remember to open the new port in the **security group / UFW**.

> **Why `systemctl edit` and not edit the service file directly?** It creates an **override** file
> (`/etc/systemd/system/jenkins.service.d/override.conf`) that survives Jenkins upgrades.

---

## Important Files and Folders

| Path | What it is |
|------|-----------|
| `/var/lib/jenkins/` | **JENKINS_HOME** — everything Jenkins stores |
| `/var/lib/jenkins/jobs/` | Job configs and build history |
| `/var/lib/jenkins/workspace/` | Where builds check out code and run |
| `/var/lib/jenkins/plugins/` | Installed plugins |
| `/var/lib/jenkins/secrets/` | Secret keys — never share |
| `/var/lib/jenkins/secrets/initialAdminPassword` | First-time unlock password |
| `/var/lib/jenkins/config.xml` | Main Jenkins configuration |
| `/usr/lib/systemd/system/jenkins.service` | The systemd service file |
| `/etc/systemd/system/jenkins.service.d/override.conf` | Your custom service settings (port etc.) |

**See Jenkins logs:**

```bash
sudo journalctl -u jenkins -f          # follow live logs (Ctrl + C to stop)
sudo journalctl -u jenkins -n 100      # last 100 lines
```
