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
