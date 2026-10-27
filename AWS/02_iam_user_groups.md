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

---

## Add Users to a Group

There are **3 ways** to add a user to a group.

### Way 1: From the Group Page

- **IAM → User groups** → click **`Developers`**
- Open the **Users** tab
- Click **Add users**
- Tick ✅ the users (e.g. `ahmed`)
- Click **Add users**

### Way 2: From the User Page

- **IAM → Users** → click the user **`ahmed`**
- Open the **Groups** tab
- Click **Add user to groups**
- Tick ✅ `Developers`
- Click **Add to group(s)**

### Way 3: While Creating a New User

- In the **Create user** wizard, on the **Set permissions** step
- Choose **Add user to group**
- Tick ✅ the group(s)

> Changes take effect **almost immediately** (sometimes a few seconds). The user may need to refresh the console page.

---

## Remove a User from a Group

### From the Group Page

- **IAM → User groups** → click **`Developers`** → **Users** tab
- Tick ✅ the user → click **Remove users**

### From the User Page

- **IAM → Users** → click the user → **Groups** tab
- Tick ✅ the group → click **Remove**

> Removing a user from a group does **not** delete the user. It only takes away the permissions that came from that group.

---

## How to Check How Many Users You Have

### 1. IAM Dashboard (fastest)

- Go to **IAM → Dashboard**
- Look at the **IAM resources** box. It shows counts like:

```
IAM resources
User groups    Users    Roles    Policies    Identity providers
    3            7        12        4               0
```

### 2. Users Page

- **IAM → Users** → the heading shows the total, e.g. **Users (7)**
- The table also shows each user's **Groups**, **Last activity**, **MFA**, **Password age** and **Access key age**

### 3. Users Inside One Group

- **IAM → User groups** → the list shows a **Users** column with the count for each group
- Click a group → **Users** tab → heading shows e.g. **Users in this group (3)**

### 4. Which Groups a User Belongs To

- **IAM → Users** → click the user → **Groups** tab

### 5. Credential Report (full audit file)

A **credential report** is a CSV file listing **every** user and the status of their password, MFA and access keys.

- **IAM → Credential report** (left sidebar, under Access reports)
- Click **Download credentials report**

Open the file in Excel / Google Sheets. Useful columns:

| Column | Meaning |
|--------|---------|
| `user` | Username |
| `password_enabled` | Does the user have console access? |
| `password_last_used` | When they last logged in |
| `mfa_active` | Is MFA turned on? |
| `access_key_1_active` | Do they have an active access key? |
| `access_key_1_last_used_date` | When the key was last used |

### 6. Using AWS CLI

```bash
# Count all users
aws iam list-users --query 'length(Users)'

# Show only the usernames
aws iam list-users --query 'Users[].UserName' --output table

# Account summary — shows Users, Groups, Policies counts and limits
aws iam get-account-summary

# All users in a group
aws iam get-group --group-name Developers --query 'Users[].UserName'

# All groups a user belongs to
aws iam list-groups-for-user --user-name ali
```

---

## What is an IAM Policy?

A **policy** is a **JSON document** that says which **actions** are **allowed** or **denied** on which **resources**.

> By default, a new IAM user can do **nothing** at all.
> Every permission must be given through a policy. This is called **"deny by default"**.

**Simple analogy:**
A policy is like a **permission slip**:
*"ALLOW the holder to READ books from the LIBRARY."*
- **Effect** → Allow
- **Action** → Read
- **Resource** → Library

---

## Types of Policies

| Type | Who creates it | Reusable? | Example | When to use |
|------|----------------|-----------|---------|-------------|
| **AWS managed** | AWS | Yes — attach to many users/groups | `AdministratorAccess`, `ReadOnlyAccess`, `AmazonS3FullAccess` | Quick start, common needs |
| **AWS managed – job function** | AWS | Yes | `Billing`, `DatabaseAdministrator`, `NetworkAdministrator` | Common job roles |
| **Customer managed** | **You** | Yes | `S3-MyBucket-ReadOnly` | ✅ When you need exact, limited permissions |
| **Inline** | You | **No** — lives inside one user/group/role only | — | Rare, strict one-to-one cases |

**How to recognise them in the console:**
- AWS managed policies have an orange/yellow AWS icon and type **AWS managed**
- Your own policies show type **Customer managed**

> AWS managed policies are easy but often give **more** access than needed.
> As you grow, prefer **customer managed** policies that follow least privilege.

---

## Attach Policies to a Group

### Attach an Existing Policy

- **IAM → User groups** → click **`Developers`**
- Open the **Permissions** tab
- Click **Add permissions** → **Attach policies**
- Search and tick ✅ the policy, e.g. **AmazonRDSReadOnlyAccess**
- Click **Attach policies**

### Add an Inline Policy to a Group

- **Permissions** tab → **Add permissions** → **Create inline policy**
- Build the policy (Visual or JSON) → **Next**
- Give it a name → **Create policy**

### Remove a Policy from a Group

- **Permissions** tab → tick ✅ the policy → click **Remove** → confirm

### Attach a Policy Directly to a User *(not recommended for daily use)*

- **IAM → Users** → click the user → **Permissions** tab
- **Add permissions** → **Add permissions** → **Attach policies directly**
- Tick the policy → **Next** → **Add permissions**

---

## Understanding Policy JSON

Every policy follows the same structure. Here is the AWS managed policy **`AmazonS3ReadOnlyAccess`** (simplified):

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:Get*",
        "s3:List*"
      ],
      "Resource": "*"
    }
  ]
}
```

| Part | Meaning |
|------|---------|
| `Version` | Policy language version. **Always** use `"2012-10-17"` (it's not today's date — it's the latest version) |
| `Statement` | A list of rules. A policy can have one or many statements |
| `Sid` | *(optional)* Statement ID — a name to describe the rule, e.g. `"AllowS3Read"` |
| `Effect` | `"Allow"` or `"Deny"` |
| `Action` | What can be done. Format: `service:ActionName` e.g. `s3:GetObject`, `ec2:StartInstances` |
| `Resource` | Which resource the action applies to — an **ARN**, or `"*"` for all |
| `Condition` | *(optional)* Extra rules — e.g. only from a certain IP, only with MFA, only in a certain region |

### Wildcards `*`

| Value | Meaning |
|-------|---------|
| `"Action": "*"` | All actions in all services (dangerous!) |
| `"Action": "s3:*"` | All S3 actions |
| `"Action": "s3:Get*"` | All S3 actions starting with `Get` (GetObject, GetBucketPolicy...) |
| `"Resource": "*"` | Every resource |
| `"Resource": "arn:aws:s3:::my-bucket/*"` | Every object inside `my-bucket` |

### Common ARN Formats

```
S3 bucket:        arn:aws:s3:::my-bucket
S3 objects:       arn:aws:s3:::my-bucket/*
EC2 instance:     arn:aws:ec2:us-east-1:123456789012:instance/i-0abcd1234efgh5678
IAM user:         arn:aws:iam::123456789012:user/ali
IAM group:        arn:aws:iam::123456789012:group/Developers
```

---

## Create Your Own Custom Policy — Step by Step

**Goal:** Create a policy called **`S3-SmitBucket-ReadOnly`** that lets users **only read files from one bucket** named `smit-devops-files`. Then attach it to a group.

### Step 1: Open Policies

- **IAM → Policies** (left sidebar)
- Click **Create policy**

### Step 2: Choose the Editor

AWS gives you two editors:

| Editor | Best for |
|--------|----------|
| **Visual** | Beginners — click to choose service, actions, resources |
| **JSON** | When you already know or copied the JSON |

### Option A: Using the Visual Editor

- **Select a service** → search and choose **S3**
- **Actions allowed:**
  - Expand **List** → tick ✅ `ListBucket`
  - Expand **Read** → tick ✅ `GetObject`
- **Resources:** choose **Specific**
  - Next to **bucket**, click **Add ARNs** → Resource bucket name: `smit-devops-files` → **Add ARNs**
  - Next to **object**, click **Add ARNs** → Resource bucket name: `smit-devops-files`, Resource object name: tick **Any object name** → **Add ARNs**
- *(Optional)* **Request conditions** → leave empty for now
- Click **Next**

### Option B: Using the JSON Editor

- Click the **JSON** tab
- Delete what's there and paste:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ListTheBucket",
      "Effect": "Allow",
      "Action": "s3:ListBucket",
      "Resource": "arn:aws:s3:::smit-devops-files"
    },
    {
      "Sid": "ReadObjectsInTheBucket",
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::smit-devops-files/*"
    }
  ]
}
```

> **Why two statements?** `ListBucket` works on the **bucket itself** (`smit-devops-files`),
> while `GetObject` works on the **files inside** it (`smit-devops-files/*`). They need different ARNs.

- AWS checks the policy live and shows **errors / warnings** at the bottom. Fix any red errors.
- Click **Next**

### Step 3: Review and Name

- **Policy name:** `S3-SmitBucket-ReadOnly`
- **Description:** `Read-only access to the smit-devops-files bucket`
- Check the **Permissions defined in this policy** summary
- *(Optional)* add tags
- Click **Create policy**

Your policy now appears in the list with type **Customer managed**. 🎉

### Step 4: Attach it to the Group

- **IAM → User groups** → click **`Developers`** → **Permissions** tab
- **Add permissions** → **Attach policies**
- **Filter by Type** → **Customer managed**
- Tick ✅ **S3-SmitBucket-ReadOnly** → **Attach policies**

### Step 5: Test it

- Sign in as `ali` (a member of `Developers`)
- Open **S3** → you can open `smit-devops-files` and download files ✅
- Try uploading a file → **Access Denied** ❌ (correct — we only allowed reading)

### Edit a Custom Policy Later

- **IAM → Policies** → click the policy → **Edit**
- Change it in Visual or JSON → **Next** → **Save changes**

> AWS keeps up to **5 versions** of each customer managed policy.
> You can see them in the **Policy versions** tab and roll back to an older one with **Set as default**.

---

## Custom Policy Examples

### Example 1: EC2 — Only Start, Stop and View Instances

Useful for a junior person who should restart servers but not create or delete them.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ViewInstances",
      "Effect": "Allow",
      "Action": "ec2:Describe*",
      "Resource": "*"
    },
    {
      "Sid": "StartStopInstances",
      "Effect": "Allow",
      "Action": [
        "ec2:StartInstances",
        "ec2:StopInstances",
        "ec2:RebootInstances"
      ],
      "Resource": "*"
    }
  ]
}
```

### Example 2: Allow Everything, but Only in One Region

Users can only work in **Mumbai (`ap-south-1`)**. Global services like IAM still work because they don't belong to a region.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "OnlyMumbaiRegion",
      "Effect": "Deny",
      "NotAction": [
        "iam:*",
        "sts:*",
        "support:*",
        "cloudfront:*",
        "route53:*"
      ],
      "Resource": "*",
      "Condition": {
        "StringNotEquals": {
          "aws:RequestedRegion": "ap-south-1"
        }
      }
    }
  ]
}
```

> This is a **Deny** policy — it doesn't give access by itself.
> Attach it **together with** an Allow policy (e.g. `PowerUserAccess`) to limit that access to one region.

### Example 3: Block Deleting S3 Buckets

Even if a user has `AmazonS3FullAccess`, this stops them from deleting any bucket.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "NoBucketDelete",
      "Effect": "Deny",
      "Action": "s3:DeleteBucket",
      "Resource": "*"
    }
  ]
}
```

### Example 4: Allow Only from Office IP Address

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "DenyOutsideOffice",
      "Effect": "Deny",
      "Action": "*",
      "Resource": "*",
      "Condition": {
        "NotIpAddress": {
          "aws:SourceIp": "203.0.113.0/24"
        }
      }
    }
  ]
}
```

> ⚠️ Be careful with IP-based deny rules — if your IP changes, you lock yourself out.

### Example 5: Let Users Manage Their Own Password and Access Keys

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ViewPasswordPolicy",
      "Effect": "Allow",
      "Action": "iam:GetAccountPasswordPolicy",
      "Resource": "*"
    },
    {
      "Sid": "ManageOwnPasswordAndKeys",
      "Effect": "Allow",
      "Action": [
        "iam:ChangePassword",
        "iam:GetUser",
        "iam:CreateAccessKey",
        "iam:DeleteAccessKey",
        "iam:ListAccessKeys",
        "iam:UpdateAccessKey"
      ],
      "Resource": "arn:aws:iam::*:user/${aws:username}"
    }
  ]
}
```

> `${aws:username}` is a **policy variable**. AWS replaces it with the name of the user who is signed in.
> So `ali` can only change **ali's** keys, and `sara` only **sara's** keys — with one single policy.

---

## How AWS Decides: Allow or Deny?

Every time a user clicks something or runs a command, AWS checks all their policies (from the user + all their groups):

```
          Request comes in
                │
                ▼
  ┌───────────────────────────────┐
  │ Is there an explicit DENY?    │── Yes ──► ❌ DENIED  (Deny always wins)
  └───────────────┬───────────────┘
                  │ No
                  ▼
  ┌───────────────────────────────┐
  │ Is there an explicit ALLOW?   │── Yes ──► ✅ ALLOWED
  └───────────────┬───────────────┘
                  │ No
                  ▼
           ❌ DENIED  (deny by default)
```

**Three rules to remember:**
1. Everything is **denied by default**.
2. An **Allow** opens the door.
3. An explicit **Deny** closes it — and **always wins**, no matter how many Allows exist.

**Example:**
- `ali` is in group `Developers` → has `AmazonS3FullAccess` (Allow `s3:*`)
- `ali` is also in group `NoDelete` → has the "Block Deleting S3 Buckets" policy (Deny `s3:DeleteBucket`)
- Result: `ali` can do everything in S3 **except** delete buckets.

---

## Test and Audit Permissions

### IAM Policy Simulator

Test what a user can or cannot do **without** actually doing it.

- Open: `https://policysim.aws.amazon.com`
- On the left, choose **Users** → select `ali`
- Choose a service, e.g. **Amazon S3**, and select actions, e.g. `DeleteBucket`
- Click **Run Simulation**
- Result shows **allowed** or **denied** for each action ✅ / ❌

### Last Accessed (Access Advisor)

Shows which services a user/group actually **used** — helps you remove permissions nobody uses.

- **IAM → User groups** (or Users) → click the name → **Last Accessed** tab
- Services with "Not accessed in the tracking period" → consider removing that permission

### See Who Uses a Policy

- **IAM → Policies** → click the policy → **Entities attached** tab
- Shows every user, group and role that has this policy
