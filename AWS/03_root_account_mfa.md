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

---

## Method 1: Add an Authenticator App (Virtual MFA)

### Step 1: Sign In as the Root User

- Go to `https://console.aws.amazon.com`
- Choose **Root user**
- Enter your **root user email address** → **Next**
- Complete the **security check** (captcha) if asked
- Enter your **password** → **Sign in**

### Step 2: Open Security Credentials

- Click your **account name** in the top-right corner of the console
- Click **Security credentials**

You are now on the page **My security credentials (root user)**.

> You can also reach this page from **IAM → Dashboard** → the **Security recommendations** box → **Add MFA** next to "Add MFA for root user".

### Step 3: Start Assigning the MFA Device

- Scroll to the section **Multi-factor authentication (MFA)**
- Click **Assign MFA device**

### Step 4: Choose the Device

- **Device name:** give it a name you'll recognise, e.g. `root-taimoor-phone`

> Device names can contain letters, numbers and `+ = , . @ - _` (up to 128 characters).
> Use a name that tells you **which** phone/device it is — helpful when you have backups.

- **MFA device:** select **Authenticator app**
- Click **Next**

### Step 5: Install the App on Your Phone

If you haven't already, install **Google Authenticator** or **Microsoft Authenticator** from the Play Store / App Store.

### Step 6: Scan the QR Code

- On the AWS page, click **Show QR code**
- Open the authenticator app on your phone
- Tap **+** (Add) → **Scan a QR code**
- Point your phone camera at the QR code on the screen

The app now shows a new entry like **Amazon Web Services (root-account-mfa-device@123456789012)** with a 6-digit code that changes every 30 seconds.

> **Can't scan?** Click **Show secret key** on AWS and type that key manually into the app
> (in the app choose **Enter a setup key** → paste the key → choose **Time based**).

### Step 7: Enter Two Codes

AWS asks for **two consecutive** codes to make sure your phone's clock is in sync:

- **MFA code 1:** type the 6-digit code shown in the app now, e.g. `482913`
- **Wait** for the code to change (up to 30 seconds)
- **MFA code 2:** type the **next** new code, e.g. `105377`
- Click **Add MFA**

```
 App shows:  482913   ──(wait 30 sec)──►   105377
               │                              │
           MFA code 1                     MFA code 2
```

> ⚠️ Don't enter the same code twice and don't wait too long after the second code — submit right away.

### Step 8: Done ✅

You will see a green message: **"MFA device assigned"**.
The **Multi-factor authentication (MFA)** section now lists your device with its **identifier**, **type** and **creation date**.

---

## Method 2: Add a Passkey or Security Key

A **passkey** uses your phone's or laptop's built-in fingerprint/face/PIN. A **security key** is a small USB/NFC device like a **YubiKey**.

### Steps

- Sign in as **root user** → account name (top right) → **Security credentials**
- **Multi-factor authentication (MFA)** → **Assign MFA device**
- **Device name:** e.g. `root-yubikey` or `root-laptop-passkey`
- **MFA device:** select **Passkey or security key**
- Click **Next**
- Your **browser** opens a pop-up asking where to save the passkey. Choose one:
  - **This device** → uses Windows Hello (PIN / face / fingerprint) or Mac Touch ID
  - **iPhone, iPad or Android device** → scan the QR code with your phone, then confirm with fingerprint/face
  - **Security key** → plug in your YubiKey and **touch** it when it blinks
- Follow the prompts → AWS shows **"MFA device assigned"** ✅

> Passkeys are **phishing resistant** — they only work on the real `aws.amazon.com` website,
> so a fake login page can't steal them.

---

## Method 3: Add a Hardware TOTP Token

A **hardware TOTP token** is a small keyfob that shows a 6-digit code when you press its button. You buy it from AWS-supported vendors.

### Steps

- Sign in as **root user** → **Security credentials**
- **Multi-factor authentication (MFA)** → **Assign MFA device**
- **Device name:** e.g. `root-hardware-token`
- **MFA device:** select **Hardware TOTP token** → **Next**
- **Serial number:** type the serial number printed on the **back** of the token
- **MFA code 1:** press the button → enter the 6-digit code
- Wait 30 seconds → press again
- **MFA code 2:** enter the new code
- Click **Add MFA** ✅

---

## Sign In as Root with MFA

From now on, signing in as root works like this:

- Go to `https://console.aws.amazon.com` → choose **Root user**
- Enter **email** → **Next** → enter **password** → **Sign in**
- AWS shows **Multi-factor authentication** screen:
  - **Authenticator app:** open the app → type the current 6-digit code → **Submit**
  - **Passkey / security key:** touch the key or confirm with fingerprint/face
  - If you have several devices, AWS lets you choose which one to use
- You are in ✅

---

## Add a Backup MFA Device

If you lose your only phone, signing in becomes difficult. So register a **second** device:

- Repeat **Method 1** or **Method 2** with a **different** device name, e.g. `root-backup-yubikey`
- Now you can sign in with **either** device

**Good backup combinations:**

| Main device | Backup device |
|-------------|---------------|
| Phone authenticator app | YubiKey security key |
| Laptop passkey | Phone authenticator app |
| YubiKey #1 | YubiKey #2 kept in a safe place |

> Apps like **Authy** and **Microsoft Authenticator** can also back up your codes to the cloud,
> so if you change phones you can restore them.

---

## Lost Your MFA Device? (Recovery)

If your phone is lost, broken or reset and you have **no backup device**:

- Go to the root sign-in page → enter email and password
- On the MFA screen, click **Troubleshoot MFA** / **Sign in using alternative factors**
- **Step 1 — Email verification:** AWS sends an email to your root email address → click the link in it
- **Step 2 — Phone verification:** AWS calls the **phone number** on your account → enter the code shown on screen
- After both steps, you are signed in → go to **Security credentials** → remove the old device and add a new one

> ⚠️ This only works if your **root email** and **account phone number** are correct and you still have access to them.
> Keep them up to date: account name (top right) → **Account** → **Contact information**.
> If even this fails, you must contact **AWS Support** and prove ownership of the account.

---

## Remove or Replace an MFA Device

**When changing phones**, add the new device **first**, then remove the old one.

- Sign in as root → **Security credentials**
- **Multi-factor authentication (MFA)** → tick ✅ the old device
- Click **Remove** → confirm **Remove**

### Resync an Authenticator App

If codes keep failing (usually because the phone clock is wrong):

- First, on your phone, turn on **automatic date & time** in settings
- In AWS: **Security credentials** → tick the device → **Resync**
- Enter two consecutive codes → **Resync**
