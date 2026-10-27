# AWS IAM: How to Add MFA for IAM Users

---

## Table of Contents
1. [Quick Recap: What is MFA?](#quick-recap-what-is-mfa)
2. [Root MFA vs IAM User MFA](#root-mfa-vs-iam-user-mfa)
3. [Two Ways to Set Up MFA for IAM Users](#two-ways-to-set-up-mfa-for-iam-users)
4. [Way 1: Admin Assigns MFA to a User](#way-1-admin-assigns-mfa-to-a-user)
5. [Way 2: User Sets Up Their Own MFA](#way-2-user-sets-up-their-own-mfa)
6. [Allow Users to Manage Their Own MFA (Policy)](#allow-users-to-manage-their-own-mfa-policy)
7. [Force Users to Use MFA (Enforce MFA Policy)](#force-users-to-use-mfa-enforce-mfa-policy)
8. [Sign In as an IAM User with MFA](#sign-in-as-an-iam-user-with-mfa)
9. [Check Which Users Have MFA](#check-which-users-have-mfa)
10. [Using MFA with AWS CLI](#using-mfa-with-aws-cli)
11. [Resync, Remove or Replace a User's MFA](#resync-remove-or-replace-a-users-mfa)
12. [User Lost Their MFA Device](#user-lost-their-mfa-device)
13. [MFA with AWS CLI Commands (Admin)](#mfa-with-aws-cli-commands-admin)
14. [Troubleshooting](#troubleshooting)
15. [Best Practices](#best-practices)
16. [Quick Reference](#quick-reference)

---

## Quick Recap: What is MFA?

**MFA (Multi-Factor Authentication)** means the user needs **two things** to sign in:
1. Their **password** (something they know)
2. A **code or touch** from a device (something they have) — phone app, passkey or YubiKey

> We already learned MFA basics and device types in `03_root_account_mfa.md`.
> This file focuses on **IAM users** — the people working in your account every day.

**Why IAM users need MFA too:**
Your admin IAM user (e.g. `taimoor-admin` with `AdministratorAccess`) is **almost as powerful as root**.
If its password leaks and there's no MFA, an attacker can do almost anything.

---

## Root MFA vs IAM User MFA

| | Root User MFA | IAM User MFA |
|---|---|---|
| Who sets it up | Root user only | Admin **or** the user themselves (if allowed) |
| Where | Account name → Security credentials | IAM → Users → (user) → Security credentials |
| Device types | Passkey/security key, Authenticator app, Hardware TOTP | Same 3 types |
| Max devices | 8 | 8 per user |
| Lost device recovery | Email + phone verification (self-service) | **Admin removes it** and user adds a new one |
| Can be forced by policy | AWS requires it | **You** enforce it with an IAM policy |

---

## Two Ways to Set Up MFA for IAM Users

```
  ┌────────────────────────────────────┐        ┌────────────────────────────────────┐
  │  Way 1: Admin assigns MFA          │        │  Way 2: User sets up own MFA       │
  │                                    │        │                                    │
  │  Admin opens the user's page and   │        │  User signs in and adds MFA from   │
  │  registers the device — user must  │        │  their own Security credentials    │
  │  be sitting next to the admin with │        │  page.                             │
  │  their phone.                      │        │                                    │
  │                                    │        │  Needs a "manage own MFA" policy.  │
  │  Good for: small teams, 1–5 users  │        │  Good for: teams, remote people ✅ │
  └────────────────────────────────────┘        └────────────────────────────────────┘
```

---

## Way 1: Admin Assigns MFA to a User

Example: the admin adds MFA for user **`ali`**. Ali must be **present with his phone**, because the QR code must be scanned on **his** phone.

### Step 1: Open the User

- Sign in as an **admin** (or root)
- Search **IAM** → open it
- Left sidebar → **Users** → click **`ali`**

### Step 2: Open Security Credentials

- Click the **Security credentials** tab
- Scroll to **Multi-factor authentication (MFA)**
- Click **Assign MFA device**

### Step 3: Choose Device Name and Type

- **Device name:** `ali-phone` (use the person's name + device so it's easy to identify)
- **MFA device:** choose one:
  - **Authenticator app** ← easiest for beginners
  - **Passkey or security key**
  - **Hardware TOTP token**
- Click **Next**

### Step 4: Scan the QR Code (Authenticator App)

- Ali installs **Google Authenticator** / **Microsoft Authenticator** / **Authy** on his phone
- On AWS, click **Show QR code**
- Ali opens the app → **+** → **Scan a QR code** → scans the screen
- The app now shows a 6-digit code for **Amazon Web Services (ali@123456789012)**

### Step 5: Enter Two Consecutive Codes

- **MFA code 1:** the code currently shown in Ali's app
- Wait for it to change (≈30 seconds)
- **MFA code 2:** the next code
- Click **Add MFA**

### Step 6: Done ✅

AWS shows **"MFA device assigned"**. In the **Users** list, the **MFA** column for `ali` now shows the device type instead of being empty.

> **For passkey / security key:** after choosing the type and clicking **Next**, the browser pop-up appears.
> The user must touch **their** key or use **their** fingerprint on **that** computer — so this also needs the user present.

---

## Way 2: User Sets Up Their Own MFA

This is the **better** way for teams — every user registers MFA on their own device, wherever they are.

> ⚠️ The user needs permission to do this. If they get **"Access denied"**, the admin must first attach
> the policy from the next section ([Allow Users to Manage Their Own MFA](#allow-users-to-manage-their-own-mfa-policy)).

### Steps (done by the user, e.g. `sara`)

- Sign in to the console as `sara` (sign-in URL + username + password)
- Click **your username** in the top-right corner (e.g. `sara @ 1234-5678-9012`)
- Click **Security credentials**
- In **Multi-factor authentication (MFA)** → click **Assign MFA device**
- **Device name:** `sara-phone`
- **MFA device:** **Authenticator app** → **Next**
- Click **Show QR code** → scan it with the authenticator app on your phone
- Enter **MFA code 1** → wait → enter **MFA code 2**
- Click **Add MFA** ✅

**Important:** after adding MFA, **sign out and sign in again**. The new login will ask for the MFA code, and only then will the user get full permissions (if MFA is enforced).

---

## Allow Users to Manage Their Own MFA (Policy)

By default, a new user can't add their own MFA device. We create a **custom policy** that lets every user manage **only their own** password and MFA.

### Step 1: Create the Policy

- **IAM → Policies** → **Create policy**
- Click the **JSON** tab → delete everything → paste:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowViewAccountInfo",
      "Effect": "Allow",
      "Action": [
        "iam:GetAccountPasswordPolicy",
        "iam:ListVirtualMFADevices"
      ],
      "Resource": "*"
    },
    {
      "Sid": "AllowManageOwnPasswordAndUserInfo",
      "Effect": "Allow",
      "Action": [
        "iam:ChangePassword",
        "iam:GetUser"
      ],
      "Resource": "arn:aws:iam::*:user/${aws:username}"
    },
    {
      "Sid": "AllowManageOwnVirtualMFADevice",
      "Effect": "Allow",
      "Action": [
        "iam:CreateVirtualMFADevice"
      ],
      "Resource": "arn:aws:iam::*:mfa/*"
    },
    {
      "Sid": "AllowManageOwnUserMFA",
      "Effect": "Allow",
      "Action": [
        "iam:DeactivateMFADevice",
        "iam:EnableMFADevice",
        "iam:ListMFADevices",
        "iam:ResyncMFADevice"
      ],
      "Resource": "arn:aws:iam::*:user/${aws:username}"
    }
  ]
}
```

- Click **Next**
- **Policy name:** `ManageOwnMFA`
- **Description:** `Lets each IAM user manage their own password and MFA device`
- Click **Create policy**

### What Each Statement Does

| Sid | Allows |
|-----|--------|
| `AllowViewAccountInfo` | See the password rules and the list of MFA devices |
| `AllowManageOwnPasswordAndUserInfo` | Change **own** password and view **own** user details |
| `AllowManageOwnVirtualMFADevice` | Create a new virtual MFA device (the QR code) |
| `AllowManageOwnUserMFA` | Turn on, turn off, list and resync **own** MFA devices |

> `${aws:username}` is replaced by the signed-in user's name.
> So `sara` can manage **only sara's** MFA, never `ali`'s.

### Step 2: Attach it to a Group

- **IAM → User groups** → create a group called **`AllUsers`** (or use an existing group)
- **Permissions** tab → **Add permissions** → **Attach policies**
- Filter **Customer managed** → tick ✅ **ManageOwnMFA** → **Attach policies**
- Add every IAM user to this group

Now every user can set up their own MFA (Way 2). ✅

---

## Force Users to Use MFA (Enforce MFA Policy)

Allowing MFA is good, but some users will simply **never** set it up.
This policy **blocks almost everything** until the user signs in **with MFA**.

```
  User signs in WITHOUT MFA                    User signs in WITH MFA
            │                                            │
            ▼                                            ▼
  ┌──────────────────────────────┐           ┌──────────────────────────────┐
  │ Can ONLY:                    │           │ Gets ALL normal permissions  │
  │ - change own password        │           │ from their groups            │
  │ - set up own MFA device      │           │ (EC2, S3, etc.) ✅           │
  │ Everything else ❌ DENIED    │           │                              │
  └──────────────────────────────┘           └──────────────────────────────┘
```

### Step 1: Create the Enforce Policy

- **IAM → Policies** → **Create policy** → **JSON** tab → paste:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowViewAccountInfo",
      "Effect": "Allow",
      "Action": [
        "iam:GetAccountPasswordPolicy",
        "iam:ListVirtualMFADevices"
      ],
      "Resource": "*"
    },
    {
      "Sid": "AllowManageOwnPasswordAndUserInfo",
      "Effect": "Allow",
      "Action": [
        "iam:ChangePassword",
        "iam:GetUser"
      ],
      "Resource": "arn:aws:iam::*:user/${aws:username}"
    },
    {
      "Sid": "AllowManageOwnVirtualMFADevice",
      "Effect": "Allow",
      "Action": [
        "iam:CreateVirtualMFADevice"
      ],
      "Resource": "arn:aws:iam::*:mfa/*"
    },
    {
      "Sid": "AllowManageOwnUserMFA",
      "Effect": "Allow",
      "Action": [
        "iam:DeactivateMFADevice",
        "iam:EnableMFADevice",
        "iam:ListMFADevices",
        "iam:ResyncMFADevice"
      ],
      "Resource": "arn:aws:iam::*:user/${aws:username}"
    },
    {
      "Sid": "DenyAllExceptListedIfNoMFA",
      "Effect": "Deny",
      "NotAction": [
        "iam:CreateVirtualMFADevice",
        "iam:EnableMFADevice",
        "iam:GetUser",
        "iam:ChangePassword",
        "iam:ListMFADevices",
        "iam:ListVirtualMFADevices",
        "iam:ResyncMFADevice",
        "sts:GetSessionToken"
      ],
      "Resource": "*",
      "Condition": {
        "BoolIfExists": {
          "aws:MultiFactorAuthPresent": "false"
        }
      }
    }
  ]
}
```

- **Policy name:** `ForceMFA`
- **Description:** `Allow users to manage own MFA, deny everything else until signed in with MFA`
- Click **Create policy**

> This policy **already includes** everything from `ManageOwnMFA`, plus the last **Deny** statement.
> So you only need to attach **`ForceMFA`** — not both.

### Understanding the Deny Statement

| Part | Meaning |
|------|---------|
| `"Effect": "Deny"` | Block… |
| `"NotAction": [...]` | …every action **except** the ones in this list (MFA setup + password change) |
| `"aws:MultiFactorAuthPresent": "false"` | …but **only** when the user did **not** sign in with MFA |
| `BoolIfExists` | Also treats requests with **no MFA info at all** (like plain access keys) as "no MFA" |

Remember the rule from `02_iam_user_groups.md`: **explicit Deny always wins**.
So even if the user has `AdministratorAccess`, without MFA they can do nothing except set up MFA.

### Step 2: Attach it to a Group

- **IAM → User groups** → **`AllUsers`** → **Permissions** → **Add permissions** → **Attach policies**
- Tick ✅ **ForceMFA** → **Attach policies**
- Make sure every IAM user is in `AllUsers`

### Step 3: Test it

1. Sign in as a user **without** MFA → open **EC2** → you see **"You are not authorized..."** ❌
2. Go to **Security credentials** → **Assign MFA device** → set it up ✅
3. **Sign out** and **sign in again** using the MFA code
4. Open **EC2** again → works ✅

> ⚠️ **Don't lock yourself out!** Before attaching `ForceMFA` to a group that includes **your own admin user**,
> make sure your admin user **already has MFA set up**. Keep the root user (with MFA) as an emergency backup.

> ⚠️ `ForceMFA` also blocks **plain access keys** used in the CLI.
> CLI users must get a temporary session with MFA (see [Using MFA with AWS CLI](#using-mfa-with-aws-cli)).

---

## Sign In as an IAM User with MFA

- Open your sign-in URL, e.g. `https://smit-devops.signin.aws.amazon.com/console`
  *(or go to `https://console.aws.amazon.com` → **IAM user** → enter Account ID / alias)*
- Enter **IAM username** and **Password** → **Sign in**
- The **Multi-factor authentication** screen appears:
  - **Authenticator app:** type the 6-digit code from the app → **Submit**
  - **Passkey / security key:** touch the key or use fingerprint/face
  - If you have more than one device, choose which one to use
- You are signed in ✅

---

## Check Which Users Have MFA

### 1. Users List (quickest)

- **IAM → Users**
- Look at the **MFA** column:
  - Shows the device type (e.g. **Virtual**, **FIDO security key**) → MFA is **on** ✅
  - Empty / **–** → MFA is **off** ❌

> If you don't see the column, click the ⚙️ **settings** icon above the table and turn on **MFA**.

### 2. Inside One User

- **IAM → Users** → click the user → **Security credentials** tab → **Multi-factor authentication (MFA)** lists all their devices

### 3. Credential Report

- **IAM → Credential report** → **Download credentials report**
- Check the **`mfa_active`** column → `true` or `false` for each user

### 4. IAM Dashboard

- **IAM → Dashboard** → **Security recommendations** shows warnings like *"Add MFA for yourself"* or users without MFA

### 5. AWS CLI

```bash
# MFA devices of one user (empty list = no MFA)
aws iam list-mfa-devices --user-name ali

# All virtual MFA devices in the account and which user owns each
aws iam list-virtual-mfa-devices --assignment-status Assigned

# Virtual MFA devices created but NOT attached to any user (leftovers)
aws iam list-virtual-mfa-devices --assignment-status Unassigned
```

**Small script — list every user without MFA:**

```bash
#!/bin/bash
# Prints the name of every IAM user who has no MFA device

for user in $(aws iam list-users --query 'Users[].UserName' --output text); do
  count=$(aws iam list-mfa-devices --user-name "$user" --query 'length(MFADevices)')
  if [ "$count" -eq 0 ]; then
    echo "❌ No MFA: $user"
  fi
done
```

---

## Using MFA with AWS CLI

Access keys in `aws configure` do **not** include MFA. If you use the `ForceMFA` policy, normal CLI commands will be **denied**.

The fix: use your keys + MFA code to get **temporary credentials** that include MFA.

### Step 1: Find Your MFA Device ARN

- Console → your username (top right) → **Security credentials** → **Multi-factor authentication (MFA)** → copy the **Identifier**

Or from the CLI:

```bash
aws iam list-mfa-devices --user-name ali
```

It looks like:

```
arn:aws:iam::123456789012:mfa/ali-phone
```

### Step 2: Get a Temporary Session

```bash
aws sts get-session-token \
  --serial-number arn:aws:iam::123456789012:mfa/ali-phone \
  --token-code 123456 \
  --duration-seconds 43200
```

- `--token-code` → the current 6-digit code from your authenticator app
- `--duration-seconds` → how long the session lasts (`43200` = 12 hours, max is 36 hours for IAM users)

Output:

```json
{
    "Credentials": {
        "AccessKeyId": "ASIAEXAMPLETEMPKEY",
        "SecretAccessKey": "wJalrXUtnFEMI/K7MDENG/EXAMPLETEMPSECRET",
        "SessionToken": "IQoJb3JpZ2luX2VjEXAMPLE...very-long-token...",
        "Expiration": "2026-10-27T21:00:00+00:00"
    }
}
```

> Temporary keys start with **`ASIA`**. Long-term keys start with **`AKIA`**.

### Step 3: Use the Temporary Credentials

```bash
export AWS_ACCESS_KEY_ID=ASIAEXAMPLETEMPKEY
export AWS_SECRET_ACCESS_KEY=wJalrXUtnFEMI/K7MDENG/EXAMPLETEMPSECRET
export AWS_SESSION_TOKEN=IQoJb3JpZ2luX2VjEXAMPLE...very-long-token...

aws s3 ls        # works now ✅ (until the session expires)
```

> These variables only last for the **current terminal window**. When the session expires, repeat Step 2.
> To go back to your normal keys: `unset AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY AWS_SESSION_TOKEN`
