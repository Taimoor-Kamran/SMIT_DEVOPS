# Class 3: PR Workflow, Code Review & Environment Promotion

---

## 1. What is a Pull Request?

A **Pull Request (PR)** is a formal way of saying: "I have finished my work on this branch — please review it and merge it into main."

It is not a Git command — it is a feature provided by platforms like **GitHub**, **GitLab**, and **Azure DevOps**.

### Real-World Example

Imagine you are working at a company. You have finished building a new user login page on your branch `feature/login`.

You do NOT push directly to `main`. Instead, you open a Pull Request.

A PR creates a page where your teammates can:
- See exactly which lines of code you changed
- Leave comments and ask questions
- Approve or request changes
- Let automated checks (tests, linters) run before anything is merged

### The Full PR Lifecycle

```
Developer                    Platform (GitHub)             Reviewer
    |                              |                           |
    |-- push branch -------------> |                           |
    |-- open PR ------------------> |                           |
    |                              |-- notify reviewers ------> |
    |                              |<-- review comments ------- |
    |<-- fix requested changes --- |                           |
    |-- push new commits --------> |                           |
    |                              |-- run checks (CI) -------> |
    |                              |<-- approve --------------- |
    |                              |-- merge to main            |
```

---

## 2. Code Review

**Code review** is the process where a teammate reads your code before it is merged — looking for bugs, security issues, unclear logic, or anything that does not follow team standards.

> Think of it like proofreading an essay before submitting — a second pair of eyes catches things you missed.

### What a Reviewer Looks For

| Category | Example |
|----------|---------|
| **Correctness** | Does the code do what it says it does? |
| **Security** | Is user input validated? Any SQL injection risk? |
| **Readability** | Are variable names clear? Is logic easy to follow? |
| **Tests** | Does new code have tests covering it? |
| **Standards** | Does it follow the team's style guide? |

### Review Actions on GitHub

When you finish reviewing a PR on GitHub, you choose one of three outcomes:

| Action | Meaning |
|--------|---------|
| **Approve** | Code looks good — ready to merge |
| **Request Changes** | Found issues — the developer must fix before merging |
| **Comment** | Left notes or questions, but did not block the merge |

### Branch Protection Rules

Most teams set **branch protection rules** on `main` to enforce quality before any merge happens.

Common rules:
- At least 1 reviewer must approve before merging
- All automated checks (CI pipeline) must pass
- Direct pushes to `main` are blocked — everything must go through a PR
- Conversation threads must be resolved before merge

---

## 3. Environment Promotion — DEV → SIT → UAT → PROD

In professional software teams, code does not go straight from a developer's laptop to production (live users).

It travels through a series of **environments** — each one a copy of the application, used for a different purpose.

```
Code change → DEV → SIT → UAT → PROD
                ↑     ↑     ↑      ↑
           Developer  QA  Business  Real users
             tests   test  sign-off
```

### DEV — Development Environment

- **Who uses it:** Developers only
- **Purpose:** Where developers build and test their own code
- **Data:** Fake/dummy data — no real users, no real money
- **Broken often?** Yes — that is fine here, it is a sandbox

### SIT — System Integration Testing

- **Who uses it:** QA (Quality Assurance) testers
- **Purpose:** Test that all pieces of the system work together — not just individual features
- **Data:** Fake data but more realistic than DEV
- **Example:** Does the login page correctly talk to the database? Does it send the right email?

### UAT — User Acceptance Testing

- **Who uses it:** Business stakeholders, product managers, sometimes real customers
- **Purpose:** "Does this feature do what the business asked for?" — not just technical testing
- **Data:** Near-production data (anonymised real data)
- **Gate:** Business must sign off ("accept") before code can go to PROD

### PROD — Production

- **Who uses it:** Real users — the public
- **Purpose:** The live application that real customers interact with
- **Data:** Real data — real users, real money, real consequences
- **Changes here?** Only with full approval, tested pipeline, and rollback plan ready

### Putting It All Together — A Real Story

> You are a developer at a bank. You build a new "Transfer Money" feature.
>
> 1. **DEV** — You write the code and test it yourself on dummy accounts.
> 2. **SIT** — QA tests that the transfer correctly deducts from Account A and adds to Account B, emails both parties, and logs the transaction.
> 3. **UAT** — The bank manager clicks through the feature on test accounts and says "yes, this matches what we asked for."
> 4. **PROD** — The feature goes live. Real customers can now transfer real money.

### Environment Comparison Table

| | DEV | SIT | UAT | PROD |
|---|---|---|---|---|
| **Who tests** | Developer | QA team | Business / Client | Real users |
| **Data** | Dummy | Fake but realistic | Near-real | Real |
| **Broken OK?** | Yes | Sometimes | Rarely | Never |
| **Deploy speed** | Fast | Moderate | Slow | Very controlled |
| **Rollback needed?** | No | Rarely | Rarely | Yes — always planned |