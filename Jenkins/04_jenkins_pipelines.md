# Jenkins: Jobs, Pipelines and the Jenkinsfile

---

## Table of Contents
1. [Your First Job: Freestyle Project](#your-first-job-freestyle-project)
2. [Freestyle Job with Git](#freestyle-job-with-git)
3. [What is a Pipeline?](#what-is-a-pipeline)
4. [Your First Pipeline — Step by Step](#your-first-pipeline--step-by-step)
5. [Declarative Pipeline Syntax Explained](#declarative-pipeline-syntax-explained)
6. [agent — Where to Run](#agent--where-to-run)
7. [stages, stage and steps](#stages-stage-and-steps)
8. [Common Steps](#common-steps)
9. [environment — Variables](#environment--variables)
10. [Built-in Jenkins Variables](#built-in-jenkins-variables)
11. [parameters — Ask for Input Before a Build](#parameters--ask-for-input-before-a-build)
12. [when — Run a Stage Only Sometimes](#when--run-a-stage-only-sometimes)
13. [input — Wait for Approval](#input--wait-for-approval)
14. [post — Run Steps After the Build](#post--run-steps-after-the-build)
15. [options — Timeouts, Log Rotation, Timestamps](#options--timeouts-log-rotation-timestamps)
16. [triggers — Start Builds Automatically](#triggers--start-builds-automatically)
17. [Credentials — Use Secrets Safely](#credentials--use-secrets-safely)
18. [Pipeline Script from SCM (Jenkinsfile in Git)](#pipeline-script-from-scm-jenkinsfile-in-git)
19. [Multibranch Pipeline](#multibranch-pipeline)
20. [Pipeline Syntax Helper (Snippet Generator)](#pipeline-syntax-helper-snippet-generator)
21. [Reading Build Results](#reading-build-results)
22. [Incident: Pipeline Fails on Docker Permission](#incident-pipeline-fails-on-docker-permission)
23. [Quick Reference](#quick-reference)

---

## Your First Job: Freestyle Project

A **Freestyle project** is set up by filling in forms. It's the easiest way to see Jenkins run something.

### Steps

- Dashboard → **New Item**
- **Enter an item name:** `hello-freestyle`
- Choose **Freestyle project** → **OK**
- **Description:** `My first Jenkins job`
- Scroll to **Build Steps** → **Add build step** → **Execute shell**
- In **Command**, type:

```bash
echo "Hello from Jenkins!"
echo "I am running as: $(whoami)"
echo "Build number: $BUILD_NUMBER"
echo "Workspace: $WORKSPACE"
date
uptime
```

- Click **Save**
- Click **Build Now** (left menu)
- Under **Build History**, click **#1** → **Console Output**

Output:

```
Started by user admin
Running as SYSTEM
Building in workspace /var/lib/jenkins/workspace/hello-freestyle
+ echo Hello from Jenkins!
Hello from Jenkins!
+ whoami
I am running as: jenkins
+ echo Build number: 1
Build number: 1
...
Finished: SUCCESS
```

> Notice **`I am running as: jenkins`** — every command runs as the `jenkins` Linux user (`03_jenkins_users.md`).
> Lines starting with `+` show the command Jenkins ran.

---

## Freestyle Job with Git

Let's make Jenkins pull a GitHub repo and run commands on it.

- **New Item** → `smit-notes-check` → **Freestyle project** → **OK**
- **Source Code Management** → choose **Git**
  - **Repository URL:** `https://github.com/Taimoor-Kamran/SMIT_DEVOPS.git`
  - **Credentials:** none (public repo)
  - **Branch Specifier:** `*/main`
- **Build Steps → Execute shell:**

```bash
echo "Files in the repo:"
ls -la
echo "Number of markdown notes:"
find . -name "*.md" | wc -l
git log -3 --oneline
```

- **Save → Build Now → Console Output**

> If you see `git: not found`, install Git on the server: `sudo apt install -y git`.

**Freestyle is fine for small tasks**, but its settings live only inside Jenkins. For real CI/CD, use a **Pipeline**.

---

## What is a Pipeline?

A **Pipeline** is a job written as **code**. The code describes **stages** that run one after another.

```
 ┌──────────┐   ┌──────────┐   ┌──────────┐   ┌──────────┐
 │ Checkout │──►│  Build   │──►│   Test   │──►│  Deploy  │
 └──────────┘   └──────────┘   └──────────┘   └──────────┘
      ✅             ✅             ❌  ← pipeline stops here, Deploy never runs
```

If **any** stage fails, the pipeline **stops** — so broken code never reaches Deploy.

There are two styles:

| Style | Starts with | Notes |
|-------|------------|-------|
| **Declarative** | `pipeline { ... }` | ✅ Simple, structured, recommended — **we use this** |
| **Scripted** | `node { ... }` | Full Groovy programming, more flexible, harder for beginners |

---

## Your First Pipeline — Step by Step

- **New Item** → `hello-pipeline` → choose **Pipeline** → **OK**
- Scroll to the **Pipeline** section
- **Definition:** `Pipeline script`
- In the **Script** box paste:

```groovy
pipeline {
    agent any

    stages {
        stage('Hello') {
            steps {
                echo 'Hello from my first pipeline!'
            }
        }
        stage('System Info') {
            steps {
                sh 'whoami'
                sh 'uname -a'
                sh 'df -h /'
            }
        }
        stage('Goodbye') {
            steps {
                echo "Build #${env.BUILD_NUMBER} finished"
            }
        }
    }
}
```

- Click **Save** → **Build Now**

On the job page you'll see each **stage** as a box with its time and status. Click a stage to see its logs, or open **Console Output** for the full log. 🎉

> The Script box has a **"try sample Pipeline..."** dropdown — choose **Hello World** to get a starter.

---

## Declarative Pipeline Syntax Explained

Here's the **full shape** of a declarative pipeline. You don't need every part — only `pipeline`, `agent` and `stages` are required.

```groovy
pipeline {                         // 1. everything goes inside this block
    agent any                      // 2. WHERE to run

    environment { }                // 3. variables for all stages
    parameters { }                 // 4. inputs asked before the build
    options { }                    // 5. timeout, log rotation, timestamps
    triggers { }                   // 6. start automatically (schedule, polling)
    tools { }                      // 7. auto-install tools (Maven, JDK, Node)

    stages {                       // 8. the list of stages
        stage('Build') {
            when { }               //    (optional) run only if...
            steps {                //    the commands
                sh 'make build'
            }
        }
    }

    post { }                       // 9. what to do after: success, failure, always
}
```

| Part | Required? | What it does |
|------|-----------|-------------|
| `pipeline` | ✅ | Wraps the whole declarative pipeline |
| `agent` | ✅ | Which machine runs it |
| `stages` | ✅ | Contains one or more `stage` blocks |
| `stage('Name')` | ✅ (at least one) | A named step of the pipeline |
| `steps` | ✅ (inside each stage) | The commands to run |
| `environment` | Optional | Variables |
| `parameters` | Optional | Build inputs |
| `options` | Optional | Pipeline settings |
| `triggers` | Optional | Automatic starts |
| `tools` | Optional | Tools configured in **Manage Jenkins → Tools** |
| `when` | Optional (in a stage) | Condition for a stage |
| `post` | Optional | After-build actions |

> **Groovy tips:** `//` starts a comment. Text in `'single quotes'` is used as-is.
> Text in `"double quotes"` replaces `${variables}` with their values.

---

## agent — Where to Run

| Agent | Meaning |
|-------|---------|
| `agent any` | Run on **any** available machine (on a learning server, the built-in node) |
| `agent none` | No default — each stage chooses its own agent |
| `agent { label 'linux' }` | Run on a machine with the label `linux` |
| `agent { docker { image 'node:20-alpine' } }` | Run the steps **inside a Docker container** (needs the **Docker Pipeline** plugin) |

**Example — run steps inside a Python container:**

```groovy
pipeline {
    agent {
        docker { image 'python:3.12-slim' }
    }
    stages {
        stage('Check Python') {
            steps {
                sh 'python --version'
            }
        }
    }
}
```

> With a Docker agent, you don't need Python installed on the server — Jenkins pulls the image and runs the steps in it.
