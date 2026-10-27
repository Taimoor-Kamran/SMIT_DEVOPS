# AWS Root Account: MFA and Other Authentication Methods

---

## Table of Contents
1. [What is the Root User?](#what-is-the-root-user)
2. [What is MFA?](#what-is-mfa)
3. [Why the Root User MUST Have MFA](#why-the-root-user-must-have-mfa)
4. [Types of MFA Devices in AWS](#types-of-mfa-devices-in-aws)
5. [Before You Start](#before-you-start)
6. [Method 1: Add an Authenticator App (Virtual MFA)](#method-1-add-an-authenticator-app-virtual-mfa)
7. [Method 2: Add a Passkey or Security Key](#method-2-add-a-passkey-or-security-key)
8. [Method 3: Add a Hardware TOTP Token](#method-3-add-a-hardware-totp-token)
9. [Sign In as Root with MFA](#sign-in-as-root-with-mfa)
10. [Add a Backup MFA Device](#add-a-backup-mfa-device)
11. [Lost Your MFA Device? (Recovery)](#lost-your-mfa-device-recovery)
12. [Remove or Replace an MFA Device](#remove-or-replace-an-mfa-device)
13. [Other Ways to Secure the Root Account](#other-ways-to-secure-the-root-account)
14. [Other Authentication Methods Available in AWS](#other-authentication-methods-available-in-aws)
15. [Root User Security Checklist](#root-user-security-checklist)
16. [Quick Reference](#quick-reference)

---

## What is the Root User?

The **root user** is the identity created when you **first sign up** for AWS.
It logs in with the **email address** and **password** you used during sign-up.

The root user has **complete, unlimited access** to everything in the account — and this power **cannot be limited** with IAM policies.

**Things only the root user can do** (a few examples):
- Close the AWS account
- Change the account's email address, name or root password
- Change the AWS Support plan
- Restore IAM permissions if the only admin locks themselves out
- Register as a seller in the Reserved Instance Marketplace

> Because it is so powerful, use the root user **only** for these rare tasks.
> For daily work, create an IAM admin user (see `01_iam_users.md`).

---

## What is MFA?

**MFA** stands for **Multi-Factor Authentication**.
It means you need **two different things** to log in instead of just one password.

| Factor | Meaning | Example |
|--------|---------|---------|
| Something you **know** | A secret in your head | Password |
| Something you **have** | A physical device | Mobile phone app, YubiKey |
| Something you **are** | Your body | Fingerprint, Face ID |

**Simple analogy:**
Your password is the **key** to your house. MFA is a **security guard** at the door who also checks your **ID card**.
Even if a thief steals your key (password), the guard won't let them in without your ID card (phone).

```
 Without MFA:   Password  ───────────────────────────────►  ✅ Logged in

 With MFA:      Password  ──►  6-digit code from phone  ──►  ✅ Logged in
                    │                     │
             (something you know)  (something you have)
```

---

## Why the Root User MUST Have MFA

- If someone gets your root password (phishing, data leak, reused password), they can:
  - Launch hundreds of expensive servers for **crypto mining** → you get a bill of **thousands of dollars**
  - Delete all your data and backups
  - Change the email and **take the account away** from you
- With MFA, a stolen password **alone is useless**.

> **AWS now requires MFA for root users.** If your root user doesn't have MFA,
> AWS will ask you to register a device when you sign in. Set it up yourself first — it only takes 2 minutes.

---

## Types of MFA Devices in AWS

AWS supports **3 types** of MFA devices:

| Type | What it is | Examples | Cost | Security |
|------|-----------|----------|------|----------|
| **Passkey or security key** | Uses FIDO2 standard. You touch a key or use fingerprint/face | YubiKey, Google Titan Key, iPhone/Android passkey, Windows Hello, Touch ID | Free (built-in passkey) or ~$25–$60 (hardware key) | ⭐⭐⭐ Highest — phishing resistant |
| **Authenticator app** (virtual MFA) | A phone app that shows a new 6-digit code every 30 seconds | Google Authenticator, Microsoft Authenticator, Authy, Duo Mobile | Free | ⭐⭐ Good |
| **Hardware TOTP token** | A small physical keyfob with a screen that shows 6-digit codes | Thales / Gemalto tokens | ~$15–$25 | ⭐⭐ Good |

> **For beginners:** use an **Authenticator app** — it is free and works on any phone.
> **For best security:** use a **passkey or security key**.
> **SMS (text message) codes are NOT supported** by AWS for MFA.

**How many devices?** You can register up to **8 MFA devices** on the root user (and on each IAM user).
You only need **one** of them to sign in. Adding a second one as a backup is a great idea.

---

## Before You Start

You need:
- Your **root email** and **password**
- A smartphone with an authenticator app installed:
  - **Google Authenticator** (Android / iPhone)
  - **Microsoft Authenticator** (Android / iPhone)
  - **Authy** (Android / iPhone / Desktop)
