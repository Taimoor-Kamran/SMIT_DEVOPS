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
