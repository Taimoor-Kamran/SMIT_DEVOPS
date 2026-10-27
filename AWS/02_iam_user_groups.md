# AWS IAM: User Groups, Policies and Custom Policies

---

## Table of Contents
1. [What is an IAM User Group?](#what-is-an-iam-user-group)
2. [Why Use Groups?](#why-use-groups)
3. [Rules and Limits of Groups](#rules-and-limits-of-groups)
4. [Create a User Group — Step by Step](#create-a-user-group--step-by-step)
5. [Add Users to a Group](#add-users-to-a-group)
6. [Remove a User from a Group](#remove-a-user-from-a-group)
7. [How to Check How Many Users You Have](#how-to-check-how-many-users-you-have)
8. [What is an IAM Policy?](#what-is-an-iam-policy)
9. [Types of Policies](#types-of-policies)
10. [Attach Policies to a Group](#attach-policies-to-a-group)
11. [Understanding Policy JSON](#understanding-policy-json)
12. [Create Your Own Custom Policy — Step by Step](#create-your-own-custom-policy--step-by-step)
13. [Custom Policy Examples](#custom-policy-examples)
14. [How AWS Decides: Allow or Deny?](#how-aws-decides-allow-or-deny)
15. [Test and Audit Permissions](#test-and-audit-permissions)
16. [Delete a Group](#delete-a-group)
17. [Groups and Policies with AWS CLI](#groups-and-policies-with-aws-cli)
18. [Best Practices](#best-practices)
19. [Quick Reference](#quick-reference)

---

## What is an IAM User Group?

An **IAM user group** is a collection of IAM users.
When you attach a permission (policy) to a group, **every user in that group** automatically gets that permission.

**Simple analogy:**
Think of a school.
- **Users** are the **students**.
- **Groups** are the **classes** — e.g. "Class 10-A", "Science Club".
- **Policies** are the **rules** of each class — "Science Club members can enter the lab".

If a new student joins the Science Club, you don't write new rules for them. You just **add them to the club**, and they get lab access automatically.

```
                    ┌──────────────────────────────┐
                    │   Policy: AmazonEC2FullAccess│
                    └──────────────┬───────────────┘
                                   │ attached to
                                   ▼
                    ┌──────────────────────────────┐
                    │     Group: Developers        │
                    └──────────────┬───────────────┘
              ┌────────────────────┼────────────────────┐
              ▼                    ▼                    ▼
         ┌─────────┐          ┌─────────┐          ┌─────────┐
         │   ali   │          │  sara   │          │  ahmed  │
         └─────────┘          └─────────┘          └─────────┘
       (all 3 users get EC2 full access through the group)
```

---

## Why Use Groups?

Imagine you have **50 developers**, and each one needs the same 5 policies.

| Without groups | With groups |
|----------------|-------------|
| Attach 5 policies × 50 users = **250 actions** | Attach 5 policies to 1 group + add 50 users |
| New developer joins → attach 5 policies again | New developer joins → add to group (1 click) |
| Developer changes team → find and remove each policy | Move them from one group to another |
| Hard to see who has what | Open the group and see everything in one place |

---

## Rules and Limits of Groups

| Rule | Detail |
|------|--------|
| A group contains **only users** | You cannot put a group inside another group (no nested groups) |
| A user can be in **many groups** | Up to **10 groups** per user |
| Permissions **add up** | If `ali` is in `Developers` and `Testers`, he gets permissions from **both** |
| Groups can't sign in | A group is not an identity — it has no password or access keys |
| Max groups per account | **300** (default limit) |
| Max managed policies per group | **10** (default limit, can be increased to 20) |
| Group names | Must be unique in the account, letters/numbers and `+ = , . @ _ -` |

---

## Create a User Group — Step by Step

In this example we will create a group called **`Developers`**.

### Step 1: Open User Groups

- Sign in to the AWS Console (as root or an admin user)
- Search for **IAM** in the top search bar → open it
- In the left sidebar under **Access management**, click **User groups**
- Click **Create group**

### Step 2: Name the Group

- **User group name:** `Developers`

> Use clear names that describe the **job role**: `Developers`, `Admins`, `Testers`, `DevOps`, `Billing`, `ReadOnly`.

### Step 3: Add Users to the Group *(optional)*

- You will see a list of all existing IAM users
- Tick ✅ the users you want to add, e.g. `ali` and `sara`

You can skip this and add users later.

### Step 4: Attach Permissions Policies *(optional)*

- In the search box type `EC2`
- Tick ✅ **AmazonEC2FullAccess**
- Search `S3` and tick ✅ **AmazonS3ReadOnlyAccess**

You can use the **Filter by Type** dropdown to show only:
- **AWS managed** (made by AWS)
- **Customer managed** (made by you)
- **AWS managed - job function** (made by AWS for common roles like `Billing`, `DatabaseAdministrator`)

### Step 5: Create

- Click **Create user group**

The group `Developers` now appears in the **User groups** list showing the number of **Users** and **Permissions**. 🎉

### Common Groups You Can Create for Practice

| Group Name | Policy to Attach | What members can do |
|------------|------------------|---------------------|
| `Admins` | `AdministratorAccess` | Everything (except root-only tasks) |
| `Developers` | `AmazonEC2FullAccess`, `AmazonS3FullAccess` | Manage servers and storage |
| `ReadOnly` | `ReadOnlyAccess` | View everything, change nothing |
| `Billing` | `Billing` (job function) | View and manage billing |
| `DevOps` | `PowerUserAccess` | Everything except managing IAM users/groups |
