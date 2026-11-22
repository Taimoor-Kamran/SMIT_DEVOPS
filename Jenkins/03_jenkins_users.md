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
