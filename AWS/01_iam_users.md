# AWS IAM: How to Create IAM Users (Step by Step)

---

## Table of Contents
1. [What is IAM?](#what-is-iam)
2. [Root User vs IAM User](#root-user-vs-iam-user)
3. [Types of Access an IAM User Can Have](#types-of-access-an-iam-user-can-have)
4. [Before You Start](#before-you-start)
5. [Create an IAM User — Step by Step (Console)](#create-an-iam-user--step-by-step-console)
6. [How the New User Signs In](#how-the-new-user-signs-in)
7. [Create an Account Alias (Easy Sign-in URL)](#create-an-account-alias-easy-sign-in-url)
8. [Create Access Keys for CLI Access](#create-access-keys-for-cli-access)
9. [Set a Password Policy](#set-a-password-policy)
10. [Manage Existing Users](#manage-existing-users)
11. [Create a User with AWS CLI](#create-a-user-with-aws-cli)
12. [Best Practices](#best-practices)
13. [Quick Reference](#quick-reference)

---

## What is IAM?

**IAM** stands for **Identity and Access Management**.
It is a free AWS service that controls **who** can sign in to your AWS account and **what** they are allowed to do.

IAM answers two simple questions:
- **Authentication** → *Who are you?* (username + password, MFA, access keys)
- **Authorization** → *What are you allowed to do?* (permissions / policies)

**Simple analogy:**
Think of your AWS account as an **office building**.
- The **root user** is the **owner** of the building — they have the master key to every room.
- **IAM users** are the **employees** — each one gets their own ID card.
- **Policies** are the **rules written on the ID card** — which rooms (services) each employee can open.

> IAM is a **global** service. You do not choose a region for it.
> A user you create is available in **all** regions of your account.

---

## Root User vs IAM User

When you first create an AWS account, you sign in with the **email and password** you used to sign up.
This identity is called the **root user**.

| | Root User | IAM User |
|---|---|---|
| Created when | You sign up for AWS | You (or an admin) create it inside IAM |
| Signs in with | Email address + password | Account ID (or alias) + username + password |
| Permissions | **Full access to everything** — cannot be limited | Only what you give it through policies |
| How many | Only one per account | Up to 5,000 per account |
| Can close the account / change billing plan | Yes | No |
| Should be used daily | **No** — only for rare account-level tasks | **Yes** — use this for daily work |

> **Golden rule:** Lock the root user away (strong password + MFA) and use **IAM users** for daily work.
> See `03_root_account_mfa.md` for how to protect the root user.

---

## Types of Access an IAM User Can Have

An IAM user can have one or both types of access:

| Access Type | Used For | Credentials |
|-------------|----------|-------------|
| **Console access** | Logging in to the AWS website (Management Console) in a browser | Username + Password (+ MFA) |
| **Programmatic access** | Using AWS CLI, SDKs (Python boto3, Node.js), Terraform, scripts | Access Key ID + Secret Access Key |

```
           ┌──────────────────────┐
           │      IAM User: ali   │
           └──────────┬───────────┘
          ┌───────────┴────────────┐
          ▼                        ▼
 ┌─────────────────┐     ┌──────────────────────┐
 │ Console Access  │     │ Programmatic Access  │
 │ username +      │     │ Access Key ID +      │
 │ password + MFA  │     │ Secret Access Key    │
 └─────────────────┘     └──────────────────────┘
   (Browser)               (aws cli / terraform)
```
