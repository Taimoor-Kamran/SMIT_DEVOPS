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

---

## stages, stage and steps

```groovy
stages {
    stage('Build') {
        steps {
            sh 'echo building...'
        }
    }
    stage('Test') {
        steps {
            sh 'echo testing...'
        }
    }
}
```

### Run Stages at the Same Time (parallel)

```groovy
stage('Tests') {
    parallel {
        stage('Unit Tests') {
            steps { sh 'echo running unit tests' }
        }
        stage('Lint') {
            steps { sh 'echo running lint' }
        }
    }
}
```

> Parallel stages finish faster — useful when tests take a long time.

---

## Common Steps

| Step | What it does | Example |
|------|-------------|---------|
| `echo` | Print a message | `echo 'Starting build'` |
| `sh` | Run a shell command (Linux) | `sh 'npm install'` |
| `bat` | Run a command on Windows agents | `bat 'dir'` |
| `git` | Clone a repo | `git branch: 'main', url: 'https://github.com/user/repo.git'` |
| `checkout scm` | Check out the same repo the Jenkinsfile came from | `checkout scm` |
| `dir` | Run steps inside a sub-folder | `dir('app') { sh 'ls' }` |
| `sleep` | Wait | `sleep 10` |
| `timeout` | Stop a step if it takes too long | `timeout(time: 5, unit: 'MINUTES') { sh './test.sh' }` |
| `retry` | Try again if it fails | `retry(3) { sh 'curl -f http://localhost:5000' }` |
| `archiveArtifacts` | Keep files from the build | `archiveArtifacts artifacts: 'dist/*.zip'` |
| `junit` | Show test results (needs JUnit plugin) | `junit 'reports/*.xml'` |
| `cleanWs` | Delete the workspace (Workspace Cleanup plugin) | `cleanWs()` |
| `error` | Fail the build on purpose | `error 'Config file missing!'` |

### Multi-line shell commands

```groovy
steps {
    sh '''
        echo "Line 1"
        cd app
        ls -la
    '''
}
```

> Triple quotes `'''` let you write a small bash script inside one `sh` step.

---

## environment — Variables

```groovy
pipeline {
    agent any

    environment {
        APP_NAME  = 'smit-app'
        APP_PORT  = '5000'
        IMAGE_TAG = "${APP_NAME}:${BUILD_NUMBER}"
    }

    stages {
        stage('Show Variables') {
            environment {
                STAGE_ONLY = 'only visible in this stage'
            }
            steps {
                echo "App: ${APP_NAME}, port: ${APP_PORT}"
                echo "Image: ${env.IMAGE_TAG}"
                sh 'echo "From bash: $APP_NAME $STAGE_ONLY"'
            }
        }
    }
}
```

| Where | Scope |
|-------|-------|
| `environment` at top | All stages |
| `environment` inside a stage | Only that stage |
| `${NAME}` in Groovy `"double quotes"` | Groovy fills it in |
| `$NAME` inside `sh '...'` | Bash reads it from the environment |

---

## Built-in Jenkins Variables

Jenkins gives every build these variables automatically:

| Variable | Example value | Meaning |
|----------|---------------|---------|
| `BUILD_NUMBER` | `14` | Build number |
| `JOB_NAME` | `smit-app-pipeline` | Job name |
| `BUILD_URL` | `http://3.110.25.40:8080/job/smit-app-pipeline/14/` | Link to this build |
| `WORKSPACE` | `/var/lib/jenkins/workspace/smit-app-pipeline` | Build folder |
| `JENKINS_URL` | `http://3.110.25.40:8080/` | Jenkins address |
| `BRANCH_NAME` | `main` | Branch (multibranch pipelines only) |
| `GIT_COMMIT` | `a1b2c3d...` | Commit being built (after checkout from Git) |
| `NODE_NAME` | `built-in` | Machine running the build |

> See the full list on any pipeline job: **Pipeline Syntax → Global Variables Reference**.

---

## parameters — Ask for Input Before a Build

Parameters let a person choose options when they click **Build with Parameters**.

```groovy
pipeline {
    agent any

    parameters {
        choice(name: 'ENVIRONMENT', choices: ['dev', 'sit', 'uat', 'prod'], description: 'Where to deploy')
        string(name: 'VERSION', defaultValue: '1.0.0', description: 'App version')
        booleanParam(name: 'RUN_TESTS', defaultValue: true, description: 'Run tests?')
    }

    stages {
        stage('Info') {
            steps {
                echo "Deploying version ${params.VERSION} to ${params.ENVIRONMENT}"
            }
        }
        stage('Test') {
            when { expression { params.RUN_TESTS } }
            steps {
                echo 'Running tests...'
            }
        }
    }
}
```

> ⚠️ After adding parameters, run the job **once** with **Build Now** — Jenkins reads the new parameters on that run.
> After that, the button changes to **Build with Parameters**.

| Parameter type | Shows as |
|----------------|---------|
| `string` | Text box |
| `text` | Big multi-line text box |
| `choice` | Dropdown |
| `booleanParam` | Checkbox |
| `password` | Hidden text box |

---

## when — Run a Stage Only Sometimes

```groovy
stage('Deploy to PROD') {
    when {
        branch 'main'                      // only on the main branch (multibranch)
    }
    steps {
        echo 'Deploying to production'
    }
}
```

| Condition | Runs the stage when... |
|-----------|----------------------|
| `branch 'main'` | Building the `main` branch (multibranch pipelines) |
| `expression { params.ENVIRONMENT == 'prod' }` | The Groovy expression is true |
| `environment name: 'DEPLOY', value: 'yes'` | A variable has a value |
| `not { branch 'main' }` | NOT on `main` |
| `allOf { ... }` / `anyOf { ... }` | All / any of several conditions |

---

## input — Wait for Approval

Stop the pipeline and wait for a person to click **Proceed** — great before deploying to production.

```groovy
stage('Approve PROD') {
    steps {
        input message: 'Deploy to PRODUCTION?', ok: 'Yes, deploy'
    }
}
```

The build pauses and shows **Yes, deploy** / **Abort** buttons on the stage. This is how teams add a **manual approval gate** (Continuous **Delivery**).

> Wrap it in a timeout so a forgotten build doesn't wait forever:
> `timeout(time: 1, unit: 'HOURS') { input message: 'Deploy to PRODUCTION?' }`

---

## post — Run Steps After the Build

`post` runs **after** all stages, depending on the result.

```groovy
pipeline {
    agent any
    stages {
        stage('Build') {
            steps { sh 'echo building' }
        }
    }
    post {
        success {
            echo '✅ Build succeeded!'
        }
        failure {
            echo '❌ Build failed — check the logs.'
        }
        always {
            echo 'This runs every time.'
        }
        cleanup {
            cleanWs()       // delete workspace at the very end
        }
    }
}
```

| Condition | Runs when |
|-----------|----------|
| `always` | Every time, whatever the result |
| `success` | Build passed |
| `failure` | Build failed |
| `unstable` | Build passed but tests were unstable |
| `aborted` | Someone stopped the build |
| `changed` | Result is different from the last build |
| `fixed` | Last build failed, this one passed |
| `regression` | Last build passed, this one failed/unstable |
| `unsuccessful` | Not successful (failure, unstable or aborted) |
| `cleanup` | Last of all, after every other `post` condition |

> Real teams send **Slack or email** messages in `failure` and `fixed`.
