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
