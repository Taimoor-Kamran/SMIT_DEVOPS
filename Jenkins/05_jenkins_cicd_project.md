# Jenkins: Full CI/CD Project — GitHub → Jenkins → Docker

---

## Table of Contents
1. [What We Will Build](#what-we-will-build)
2. [Before You Start](#before-you-start)
3. [Step 1: Create the App Repository](#step-1-create-the-app-repository)
4. [Step 2: Write the App](#step-2-write-the-app)
5. [Step 3: Write the Dockerfile](#step-3-write-the-dockerfile)
6. [Step 4: Write the Jenkinsfile](#step-4-write-the-jenkinsfile)
7. [Step 5: Push to GitHub](#step-5-push-to-github)
8. [Step 6: Create the Pipeline Job in Jenkins](#step-6-create-the-pipeline-job-in-jenkins)
9. [Step 7: Run the First Build](#step-7-run-the-first-build)
10. [Step 8: Auto-Build on Every Push (GitHub Webhook)](#step-8-auto-build-on-every-push-github-webhook)
11. [Step 9: Test the Full Flow](#step-9-test-the-full-flow)
12. [Bonus: Push the Image to Docker Hub](#bonus-push-the-image-to-docker-hub)
13. [Incident: Webhook Not Triggering Builds](#incident-webhook-not-triggering-builds)
14. [Incident: Port Already Allocated](#incident-port-already-allocated)
15. [Clean Up](#clean-up)
16. [Quick Reference](#quick-reference)

---

## What We Will Build

A real CI/CD pipeline: every `git push` automatically **builds**, **tests** and **deploys** a small web app in Docker.

```
  You                GitHub                  Jenkins (EC2 / Ubuntu)              Browser
   │                   │                            │                              │
   │── git push ──────►│                            │                              │
   │                   │── webhook ────────────────►│                              │
   │                   │                            │ 1. Checkout code             │
   │                   │◄──── git clone ────────────│ 2. Build Docker image        │
   │                   │                            │ 3. Test the image            │
   │                   │                            │ 4. Deploy (run container)    │
   │                   │                            │ 5. Smoke test (curl)         │
   │                   │                            │                              │
   │                   │                            │◄──── http://<ip>:5000 ───────│
   │                   │                            │───── "Hello SMIT v1" ───────►│
```

**Skills used:** Linux (`Phase_1`), Git & GitHub (`Phase_2`), Bash (`Phase_3`), Docker (`Phase_4`), AWS EC2, and Jenkins (files 01–04 in this folder).

---

## Before You Start

Make sure you have:

- [ ] Jenkins installed and running (`02_jenkins_installation_ubuntu.md`)
- [ ] **Git** and **Docker** installed on the Jenkins server
- [ ] The `jenkins` user in the `docker` group (`03_jenkins_users.md`)
- [ ] A GitHub account
- [ ] On EC2: security group allows **8080** (Jenkins) and **5000** (the app) from **My IP**

**Quick check on the server:**

```bash
sudo systemctl status jenkins --no-pager | head -3
git --version
sudo -u jenkins docker ps
```

All three should work without errors.

---

## Step 1: Create the App Repository

- Go to **GitHub → New repository**
- **Repository name:** `jenkins-smit-app`
- **Public** (easier for the first project)
- Tick **Add a README file**
- Click **Create repository**

Clone it on your **own computer** (or WSL):

```bash
git clone https://github.com/<your-user>/jenkins-smit-app.git
cd jenkins-smit-app
```

---

## Step 2: Write the App

Create **`app.py`**:

```python
from flask import Flask

app = Flask(__name__)
VERSION = "v1"


@app.route("/")
def home():
    return f"Hello SMIT {VERSION} - deployed by Jenkins!\n"


@app.route("/health")
def health():
    return "OK\n"


if __name__ == "__main__":
    # 0.0.0.0 = listen on all network interfaces, so it works inside Docker
    app.run(host="0.0.0.0", port=5000)
```

Create **`requirements.txt`**:

```
flask==3.0.3
```

> `/health` is a **health-check endpoint** — a simple URL that says "I'm alive". Pipelines and load balancers use it to test the app.

---

## Step 3: Write the Dockerfile

Create **`Dockerfile`**:

```dockerfile
FROM python:3.12-slim

WORKDIR /app

# Install dependencies first (cached layer — see Phase_4/class1.md)
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy the app code last (changes most often)
COPY app.py .

EXPOSE 5000

CMD ["python", "app.py"]
```

> We put `requirements.txt` before `app.py` so Docker **reuses the cached layer** when only the code changes — faster builds.

---

## Step 4: Write the Jenkinsfile

Create **`Jenkinsfile`** (no file extension):

```groovy
pipeline {
    agent any

    environment {
        APP_NAME  = 'smit-app'
        APP_PORT  = '5000'
        IMAGE     = "smit-app:${BUILD_NUMBER}"
    }

    options {
        timeout(time: 15, unit: 'MINUTES')
        buildDiscarder(logRotator(numToKeepStr: '10'))
        timestamps()
        disableConcurrentBuilds()
    }

    triggers {
        githubPush()        // build when GitHub sends a webhook
    }

    stages {
        stage('Info') {
            steps {
                sh 'git log -1 --oneline'
                sh 'docker --version'
            }
        }

        stage('Build Image') {
            steps {
                sh 'docker build -t "$IMAGE" .'
            }
        }

        stage('Test Image') {
            steps {
                // Start a temporary container, check /health, then remove it
                sh '''
                    docker run -d --name "${APP_NAME}-test" "$IMAGE"
                    sleep 3
                    docker exec "${APP_NAME}-test" python -c "import urllib.request; print(urllib.request.urlopen('http://localhost:5000/health').read().decode())"
                '''
            }
            post {
                always {
                    sh 'docker rm -f "${APP_NAME}-test" || true'
                }
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    docker rm -f "$APP_NAME" || true
                    docker run -d --name "$APP_NAME" --restart unless-stopped -p "$APP_PORT:5000" "$IMAGE"
                '''
            }
        }

        stage('Smoke Test') {
            steps {
                retry(5) {
                    sleep 2
                    sh 'curl -fsS "http://localhost:$APP_PORT/"'
                }
            }
        }
    }

    post {
        success {
            echo "✅ Deployed ${IMAGE} — open http://<server-ip>:${APP_PORT}"
        }
        failure {
            echo '❌ Pipeline failed — read the Console Output from the bottom up.'
        }
        always {
            sh 'docker image prune -f || true'     // remove dangling images to save disk
        }
    }
}
```

### What Each Stage Does

| Stage | What happens | If it fails... |
|-------|-------------|---------------|
| **Info** | Prints the commit and Docker version | Git or Docker isn't installed |
| **Build Image** | `docker build` makes an image tagged with the build number, e.g. `smit-app:7` | Bad Dockerfile or `requirements.txt` |
| **Test Image** | Runs a **temporary** container and checks `/health` inside it | The app crashes on start — **old version stays live** ✅ |
| **Deploy** | Removes the old container and runs the new one on port 5000 | Port problem or Docker issue |
| **Smoke Test** | `curl` the live app (retries up to 5 times) | New version isn't answering |

> **Why tag with `BUILD_NUMBER`?** Each build has its own image (`smit-app:6`, `smit-app:7`...).
> If version 7 is broken, you can quickly run `smit-app:6` again — an easy **rollback**.

> **`|| true`** means "if this command fails, don't fail the pipeline" — used when the container might not exist yet.

---

## Step 5: Push to GitHub

```bash
git add app.py requirements.txt Dockerfile Jenkinsfile
git commit -m "Add Flask app, Dockerfile and Jenkinsfile"
git push origin main
```

Your repo now looks like:

```
jenkins-smit-app/
├── Dockerfile
├── Jenkinsfile
├── README.md
├── app.py
└── requirements.txt
```

---

## Step 6: Create the Pipeline Job in Jenkins

- Open Jenkins → **New Item**
- **Name:** `jenkins-smit-app`
- Choose **Pipeline** → **OK**
- **General:** tick **GitHub project** → **Project url:** `https://github.com/<your-user>/jenkins-smit-app/`
- **Triggers:** tick **GitHub hook trigger for GITScm polling**
- **Pipeline:**
  - **Definition:** `Pipeline script from SCM`
  - **SCM:** `Git`
  - **Repository URL:** `https://github.com/<your-user>/jenkins-smit-app.git`
  - **Branch Specifier:** `*/main`
  - **Script Path:** `Jenkinsfile`
- Click **Save**

> The **GitHub hook trigger** option comes from the **GitHub** plugin (installed with suggested plugins).

---

## Step 7: Run the First Build

- Click **Build Now**
- Watch the stages turn green one by one: **Info → Build Image → Test Image → Deploy → Smoke Test**
- Open the build → **Console Output** to see every command

When it's ✅ green, open in your browser:

```
http://<server-ip>:5000
```

You should see:

```
Hello SMIT v1 - deployed by Jenkins!
```

🎉 Your app was built and deployed by Jenkins!

Check on the server:

```bash
docker ps
docker images | grep smit-app
```

> **Why click Build Now the first time?** Jenkins learns about the `triggers { githubPush() }` setting from the Jenkinsfile only **after** the first run.

---

## Step 8: Auto-Build on Every Push (GitHub Webhook)

A **webhook** is GitHub calling Jenkins and saying: **"new code was pushed — build now!"**

```
 git push ──► GitHub ──POST http://<jenkins-ip>:8080/github-webhook/──► Jenkins ──► build starts
```

### 8.1 Allow GitHub to Reach Jenkins

GitHub's servers must be able to reach your Jenkins on port 8080.

- **On AWS EC2:** in the security group, add an inbound rule for port **8080** from **GitHub's webhook IP ranges**.
  You can find them at `https://api.github.com/meta` under `"hooks"`. (Opening 8080 to `0.0.0.0/0` also works for a short practice, but **close it afterwards**.)
- **On WSL / local VM:** GitHub can't reach your laptop directly. Use **Poll SCM** instead (in the job: **Triggers → Poll SCM** → `H/2 * * * *`), or a tunnel tool like **ngrok**.

### 8.2 Add the Webhook on GitHub

- Open your repo on GitHub → **Settings** → **Webhooks** → **Add webhook**
- Fill in:

| Field | Value |
|-------|-------|
| **Payload URL** | `http://<jenkins-public-ip>:8080/github-webhook/` |
| **Content type** | `application/json` |
| **Secret** | leave empty for now |
| **Which events?** | **Just the push event** |
| **Active** | ✅ ticked |

- Click **Add webhook**

> ⚠️ The URL **must end with `/github-webhook/`** — including the last slash.

### 8.3 Check the Webhook

- Click the webhook → **Recent Deliveries** tab
- You should see a delivery with a green ✅ and **Response 200**
- A red ❌ means GitHub couldn't reach Jenkins — see the [incident below](#incident-webhook-not-triggering-builds)

---

## Step 9: Test the Full Flow

Change the version in **`app.py`**:

```python
VERSION = "v2"
```

Push it:

```bash
git add app.py
git commit -m "Bump app version to v2"
git push origin main
```

**Without clicking anything**, watch Jenkins:
- A new build starts within seconds (**Started by GitHub push by <your-user>**)
- All stages go green
- Refresh `http://<server-ip>:5000`:

```
Hello SMIT v2 - deployed by Jenkins!
```

✅ **That's CI/CD!** Code → push → automatic build, test and deploy.

### Try Breaking It (Learn from Failure)

Add a typo in `app.py` (e.g. `retrun` instead of `return`) and push. Watch:
- **Test Image** fails ❌ — the app crashes on start
- **Deploy** never runs, so **v2 stays live** — users never see the broken version
- Fix the typo, push again → ✅ green

This is exactly why pipelines have a **Test** stage before **Deploy**.

---

## Bonus: Push the Image to Docker Hub

Share your image so other servers can pull it.

1. Create a **Docker Hub** account → **Account settings → Personal access tokens → Generate new token**
2. In Jenkins: **Manage Jenkins → Credentials → System → Global credentials → Add Credentials**
   - **Kind:** Username with password
   - **Username:** your Docker Hub username
   - **Password:** the access token
   - **ID:** `dockerhub-creds`
3. Add this stage to the Jenkinsfile, **after Smoke Test**:

```groovy
        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds',
                                                  usernameVariable: 'DOCKER_USER',
                                                  passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker tag "$IMAGE" "$DOCKER_USER/smit-app:$BUILD_NUMBER"
                        docker tag "$IMAGE" "$DOCKER_USER/smit-app:latest"
                        docker push "$DOCKER_USER/smit-app:$BUILD_NUMBER"
                        docker push "$DOCKER_USER/smit-app:latest"
                        docker logout
                    '''
                }
            }
        }
```

4. Push the Jenkinsfile change → the image appears on `hub.docker.com/r/<your-user>/smit-app`

> The password shows as `****` in the logs — Jenkins masks credentials automatically.

---

## Incident: Webhook Not Triggering Builds

**Symptom:** You push to GitHub, but no build starts.

**Investigate step by step:**

| Check | How | Fix |
|-------|-----|-----|
| 1. Did GitHub send it? | Repo → Settings → Webhooks → **Recent Deliveries** | No delivery → webhook isn't active, or wrong event selected |
| 2. Did it fail? | Red ❌ + "failed to connect" or "timed out" | GitHub can't reach Jenkins → open port 8080 to GitHub's IPs in the security group |
| 3. Wrong URL? | Response **404** | URL must be `http://<ip>:8080/github-webhook/` (with the trailing `/`) |
| 4. Old IP? | EC2 was stopped and started | Public IP changed → update the webhook URL (or use an **Elastic IP**) |
| 5. Jenkins received it but didn't build? | **Manage Jenkins → System Log** | Make sure the job has **GitHub hook trigger for GITScm polling** ticked and the repo URL **matches exactly** |
| 6. Never built manually? | — | Run **Build Now** once so Jenkins registers the trigger |

**Redeliver after fixing:** GitHub → Recent Deliveries → click a delivery → **Redeliver**.
