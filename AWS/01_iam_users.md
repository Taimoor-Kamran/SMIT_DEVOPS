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

---

## Before You Start

You need:
- An AWS account (Free Tier is fine)
- Sign in as the **root user** **or** as an IAM user who already has admin permissions (`AdministratorAccess` or `IAMFullAccess`)

> If this is a brand-new account, you will do this first step as the root user.
> After you create your own **admin IAM user**, stop using the root user.

---

## Create an IAM User — Step by Step (Console)

In this example we will create a user called **`ali`** who is a developer.

### Step 1: Open the IAM Console

- Sign in to the AWS Management Console: `https://console.aws.amazon.com`
- In the **search bar** at the top, type **IAM**
- Click **IAM** from the results

You will see the **IAM Dashboard**.

### Step 2: Go to Users

- In the left sidebar, under **Access management**, click **Users**
- Click the orange **Create user** button (top right)

### Step 3: Specify User Details

Fill in:

- **User name:** `ali`

> Usernames can contain letters, numbers and these characters: `+ = , . @ _ -`
> Usernames are **not** case-sensitive, so `Ali` and `ali` count as the same user.

Now tick the checkbox:

- ✅ **Provide user access to the AWS Management Console** *(optional)*

Only tick this if the user needs to log in from the browser.
If the user only needs the CLI (for example a script or a CI/CD pipeline), leave it **unticked**.

After ticking, AWS asks: **"Are you providing console access to a person?"**

Choose:

- **I want to create an IAM user**

> AWS also shows an option called **IAM Identity Center** (recommended for big companies with many accounts).
> For learning and small accounts, a normal **IAM user** is perfect.

### Step 4: Choose the Console Password

You have two options:

| Option | What it does |
|--------|-------------|
| **Autogenerated password** | AWS creates a random strong password for you |
| **Custom password** | You type the password yourself |

Then keep this box ticked:

- ✅ **Users must create a new password at next sign-in** *(Recommended)*

This forces the user to change the password the first time they log in, so **only they** know their final password.

> When this box is ticked, AWS automatically attaches a policy called **`IAMUserChangePassword`** to the user,
> so they are allowed to change their own password.

Click **Next**.

### Step 5: Set Permissions

Now AWS asks what this user is allowed to do. There are **3 options**:

| Option | Meaning | When to use |
|--------|---------|-------------|
| **Add user to group** | User gets all permissions of the group | ✅ **Best practice** — use this most of the time |
| **Copy permissions** | Copy groups and policies from an existing user | When the new person does the same job as someone else |
| **Attach policies directly** | Attach policies only to this one user | Quick tests, or one-off special access |

**For this example (simple way):**

- Select **Attach policies directly**
- In the search box type: `ReadOnlyAccess`
- Tick ✅ **ReadOnlyAccess** (an AWS managed policy — user can view everything but change nothing)

> In real projects you should use **groups** instead of attaching policies directly.
> We cover groups in detail in `02_iam_user_groups.md`.

**Permissions boundary** *(optional)* — leave it as it is for now. It is an advanced feature that sets the **maximum** permissions a user can ever get.

Click **Next**.

### Step 6: Review and Create

AWS shows a summary page:

- User name: `ali`
- Console password type: Autogenerated
- Require password reset: Yes
- Permissions: `ReadOnlyAccess`, `IAMUserChangePassword`

**Tags** *(optional)* — tags are labels made of a **key** and a **value**. They help you organise and search users.

Example:

```
Key: Department     Value: Development
Key: Team           Value: Backend
```

Click **Create user**.

### Step 7: Retrieve the Password (Very Important!)

AWS shows a page called **Retrieve password** with:

- **Console sign-in URL** → e.g. `https://123456789012.signin.aws.amazon.com/console`
- **User name** → `ali`
- **Console password** → click **Show** to see it

You can:
- Click **Download .csv file** → saves the sign-in details to your computer
- Click **Email sign-in instructions** → opens your email app with the sign-in link and username (the password is **not** included in the email)

> ⚠️ **This is the ONLY time you can see this password.**
> If you close this page without saving it, you will have to **reset** the password later.

Click **Return to users list**. Your user `ali` now appears in the users list. 🎉

---

## How the New User Signs In

Give the user these 3 things (send the password through a **different** channel than the URL, e.g. URL by email and password by WhatsApp/phone):

1. **Sign-in URL** → `https://123456789012.signin.aws.amazon.com/console`
2. **Username** → `ali`
3. **Temporary password**

**The user does this:**

- Open the sign-in URL
- The **Account ID** is already filled in (from the URL)
- Enter **IAM username:** `ali`
- Enter **Password:** the temporary password
- Click **Sign in**
- AWS asks to set a **new password** → enter old password, new password, confirm → **Confirm password change**

Now the user is inside the AWS Console. In the top-right corner they will see `ali @ 1234-5678-9012`.

**Another way to sign in** (without the special URL):

- Go to `https://console.aws.amazon.com`
- Choose **IAM user**
- Enter the **12-digit Account ID** (or alias) → **Next**
- Enter username and password

> **Where do I find my Account ID?**
> Click your account name in the top-right corner of the console — the 12-digit Account ID is shown there.

---

## Create an Account Alias (Easy Sign-in URL)

A 12-digit number is hard to remember. You can replace it with a friendly name called an **account alias**.

```
Before:  https://123456789012.signin.aws.amazon.com/console
After:   https://smit-devops.signin.aws.amazon.com/console
```

**Steps:**

- Go to **IAM → Dashboard**
- On the right side, find **AWS Account** → **Account Alias**
- Click **Create**
- Type an alias, e.g. `smit-devops`
- Click **Save changes**

> The alias must be **unique across all of AWS** (like a username on Instagram).
> It can only contain lowercase letters, numbers, and hyphens.
