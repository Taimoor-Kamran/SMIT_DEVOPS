# Jenkins: The Jenkins Linux User and Jenkins Web Users

---

## Table of Contents
1. [Two Kinds of "Jenkins User"](#two-kinds-of-jenkins-user)
2. [Part 1: The `jenkins` Linux User](#part-1-the-jenkins-linux-user)
3. [Inspect the `jenkins` User](#inspect-the-jenkins-user)
4. [Switch to the `jenkins` User](#switch-to-the-jenkins-user)
5. [Why Builds Get "Permission Denied"](#why-builds-get-permission-denied)
6. [Give the `jenkins` User Access — the Safe Way](#give-the-jenkins-user-access--the-safe-way)
7. [Docker Access for Jenkins](#docker-access-for-jenkins)
8. [Limited sudo for Jenkins](#limited-sudo-for-jenkins)
9. [SSH Keys for the `jenkins` User](#ssh-keys-for-the-jenkins-user)
10. [Part 2: Jenkins Web Users](#part-2-jenkins-web-users)
11. [Create a Jenkins User — Step by Step](#create-a-jenkins-user--step-by-step)
12. [Security Settings: Who Can Log In](#security-settings-who-can-log-in)
13. [Authorization: What Users Can Do](#authorization-what-users-can-do)
14. [Matrix-Based Security — Step by Step](#matrix-based-security--step-by-step)
15. [Role-Based Access — Step by Step](#role-based-access--step-by-step)
16. [API Tokens](#api-tokens)
17. [Reset a Forgotten Admin Password](#reset-a-forgotten-admin-password)
18. [Best Practices](#best-practices)
19. [Quick Reference](#quick-reference)

---

## Two Kinds of "Jenkins User"

When people say "Jenkins user" they can mean **two different things**:

| | 1. `jenkins` **Linux** user | 2. Jenkins **web** users |
|---|---|---|
| What it is | A **system account** on the Ubuntu server | People who **log in to the Jenkins website** |
| Created by | The Jenkins package, automatically | You, in **Manage Jenkins → Users** |
| Used for | **Running** Jenkins and every build command | Creating jobs, clicking **Build Now**, changing settings |
| Example | `sh 'docker build .'` runs as `jenkins` | `ali` logs in to view build logs |
| Managed with | `id`, `usermod`, `chown`, `sudo` | Jenkins web UI |

```
  ali (web user) ──clicks "Build Now"──►  Jenkins  ──runs commands as──►  jenkins (Linux user)
  sara (web user) ─views logs──────────►  (web UI)                         on the Ubuntu server
```

> Even if **`ali`** starts the build, the commands on the server run as the **`jenkins`** Linux user.
> That's why the Linux user's permissions matter so much.

---

## Part 1: The `jenkins` Linux User

When you install Jenkins (`02_jenkins_installation_ubuntu.md`), the package creates a Linux user called **`jenkins`**.

This is a **service user** — the same idea you learned in `Phase_1/class_4.md`:
an account that exists to **run one program**, not for a human to log in with.

**Why not just run Jenkins as root?**

| Run as `root` | Run as `jenkins` |
|---------------|-----------------|
| A bad build script can delete **anything** on the server | A bad script can only damage what `jenkins` owns |
| A hacked Jenkins = hacked server | Damage is limited |
| ❌ Never do this | ✅ Correct |

**Simple analogy:** the `jenkins` user is like a **contractor** with a key to **one workshop** in a building — not the master key to every room.
