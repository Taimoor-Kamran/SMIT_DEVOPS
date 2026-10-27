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
