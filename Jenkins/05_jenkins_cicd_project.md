# Jenkins: Full CI/CD Project — GitHub → Jenkins → Docker

---

## Table of Contents
1. [What We Will Build](#what-we-will-build)
2. [Before You Start](#before-you-start)
3. [Step 1: Create the App Repository](#step-1-create-the-app-repository)
4. [Step 2: Write the App](#step-2-write-the-app)
5. [Step 3: Write the Dockerfile](#step-3-write-the-dockerfile)
6. [Step 4: Write the Jenkinsfile](#step-4-write-the-jenkinsfile)
7. [Step 5: Push to GitHub](#step-5-push-to-github)
8. [Step 6: Create the Pipeline Job in Jenkins](#step-6-create-the-pipeline-job-in-jenkins)
9. [Step 7: Run the First Build](#step-7-run-the-first-build)
10. [Step 8: Auto-Build on Every Push (GitHub Webhook)](#step-8-auto-build-on-every-push-github-webhook)
11. [Step 9: Test the Full Flow](#step-9-test-the-full-flow)
12. [Bonus: Push the Image to Docker Hub](#bonus-push-the-image-to-docker-hub)
13. [Incident: Webhook Not Triggering Builds](#incident-webhook-not-triggering-builds)
14. [Incident: Port Already Allocated](#incident-port-already-allocated)
15. [Clean Up](#clean-up)
16. [Quick Reference](#quick-reference)

---

## What We Will Build

A real CI/CD pipeline: every `git push` automatically **builds**, **tests** and **deploys** a small web app in Docker.

```
  You                GitHub                  Jenkins (EC2 / Ubuntu)              Browser
   │                   │                            │                              │
   │── git push ──────►│                            │                              │
   │                   │── webhook ────────────────►│                              │
   │                   │                            │ 1. Checkout code             │
   │                   │◄──── git clone ────────────│ 2. Build Docker image        │
   │                   │                            │ 3. Test the image            │
   │                   │                            │ 4. Deploy (run container)    │
   │                   │                            │ 5. Smoke test (curl)         │
   │                   │                            │                              │
   │                   │                            │◄──── http://<ip>:5000 ───────│
   │                   │                            │───── "Hello SMIT v1" ───────►│
```

**Skills used:** Linux (`Phase_1`), Git & GitHub (`Phase_2`), Bash (`Phase_3`), Docker (`Phase_4`), AWS EC2, and Jenkins (files 01–04 in this folder).

---

## Before You Start

Make sure you have:

- [ ] Jenkins installed and running (`02_jenkins_installation_ubuntu.md`)
- [ ] **Git** and **Docker** installed on the Jenkins server
- [ ] The `jenkins` user in the `docker` group (`03_jenkins_users.md`)
- [ ] A GitHub account
- [ ] On EC2: security group allows **8080** (Jenkins) and **5000** (the app) from **My IP**

**Quick check on the server:**

```bash
sudo systemctl status jenkins --no-pager | head -3
git --version
sudo -u jenkins docker ps
```

All three should work without errors.
