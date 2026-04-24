# Experiment 7: CI/CD Pipeline using Jenkins, GitHub & Docker Hub

> Design and implement a complete CI/CD pipeline using Jenkins — pulling source code from GitHub, building Docker images, and pushing them to Docker Hub automatically on every commit.

---

## Table of Contents

- [Aim & Objectives](#-aim--objectives)
- [Theory](#-theory)
- [Prerequisites](#-prerequisites)
- [Part A – GitHub Repository Setup](#-part-a--github-repository-setup)
- [Part B – Jenkins Setup with Docker](#-part-b--jenkins-setup-using-docker)
- [Part C – Jenkins Configuration](#-part-c--jenkins-configuration)
- [Part D – GitHub Webhook Integration](#-part-d--github-webhook-integration)
- [Part E – Execution Flow](#-part-e--execution-flow)
- [Understanding Jenkinsfile Syntax](#-understanding-jenkinsfile-syntax)
- [Key Notes](#-key-notes)

---

## Aim & Objectives

**Aim:** Implement a complete CI/CD pipeline using Jenkins, integrating GitHub as the source and Docker Hub as the artifact registry.

**Objectives:**
- Understand the CI/CD workflow using Jenkins (GUI-based)
- Create a GitHub repository with application code and a `Jenkinsfile`
- Build Docker images from source code
- Securely store Docker Hub credentials in Jenkins
- Automate the build & push process using webhook triggers
- Use the same Docker host as the Jenkins agent

---

## Theory

### What is Jenkins?

Jenkins is a web-based GUI automation server used to build, test, and deploy software. It provides:

- A browser-based dashboard
- A rich plugin ecosystem (GitHub, Docker, etc.)
- Pipeline as Code via `Jenkinsfile`

### What is CI/CD?

| Term | Definition |
|---|---|
| **Continuous Integration (CI)** | Code is automatically built and tested after each commit |
| **Continuous Deployment (CD)** | Built artifacts (Docker images) are automatically delivered |

### Workflow Overview

```
Developer → GitHub → Webhook → Jenkins → Build → Docker Hub
```

---

## Prerequisites

- Docker & Docker Compose installed
- GitHub account
- Docker Hub account
- Basic Linux command knowledge

---

## 📁 Part A – GitHub Repository Setup

### Project Structure

```
my-app/
├── app.py
├── requirements.txt
├── Dockerfile
└── Jenkinsfile
```

### `app.py`

```python
from flask import Flask
app = Flask(__name__)

@app.route("/")
def home():
    return "Hello from CI/CD Pipeline!"

app.run(host="0.0.0.0", port=80)
```

### `requirements.txt`

```
flask
```

### `Dockerfile`

```dockerfile
FROM python:3.10-slim

WORKDIR /app
COPY . .

RUN pip install -r requirements.txt

EXPOSE 80
CMD ["python", "app.py"]
```

### `Jenkinsfile`

```groovy
pipeline {
    agent any

    environment {
        IMAGE_NAME = "your-dockerhub-username/myapp"
    }

    stages {

        stage('Clone Source') {
            steps {
                git branch: 'main', url: 'https://github.com/your-username/my-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:latest .'
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([string(credentialsId: 'dockerhub-token', variable: 'DOCKER_TOKEN')]) {
                    sh 'echo $DOCKER_TOKEN | docker login -u your-dockerhub-username --password-stdin'
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                sh 'docker push $IMAGE_NAME:latest'
            }
        }
    }
}
```

---

## Part B – Jenkins Setup using Docker

### `docker-compose.yml`

```yaml
version: '3.8'

services:
  jenkins:
    image: jenkins/jenkins:lts
    container_name: jenkins
    restart: always
    ports:
      - "8080:8080"
      - "50000:50000"
    user: root
    volumes:
      - jenkins_home:/var/jenkins_home
      - /var/run/docker.sock:/var/run/docker.sock
      - /usr/bin/docker:/usr/bin/docker

volumes:
  jenkins_home:
```

> Mounting `/var/run/docker.sock` lets Jenkins directly control the host Docker daemon — no separate agent needed.

### Start Jenkins

```bash
docker-compose up -d
```

Access Jenkins at **http://localhost:8080**

### Unlock Jenkins

```bash
docker exec -it jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```

Paste the password in the browser, then:

1. Install suggested plugins
2. Create an admin user

---

## Part C – Jenkins Configuration

### Add Docker Hub Credentials

Navigate to:

```
Manage Jenkins → Credentials → Add Credentials
```

| Field | Value |
|---|---|
| **Type** | Secret Text |
| **ID** | `dockerhub-token` |
| **Value** | Your Docker Hub Access Token |

### Create Pipeline Job

1. **New Item → Pipeline**
2. Name: `ci-cd-pipeline`
3. Configure:
   - Pipeline script from **SCM**
   - SCM: **Git**
   - Repository URL: your GitHub repo
   - Script Path: `Jenkinsfile`

---

## Part D – GitHub Webhook Integration

### Configure Webhook in GitHub

Navigate to your repository:

```
Settings → Webhooks → Add Webhook
```

| Field | Value |
|---|---|
| **Payload URL** | `http://<your-server-ip>:8080/github-webhook/` |
| **Content type** | `application/json` |
| **Events** | Push events |

Now every `git push` automatically triggers the Jenkins pipeline.

---

## Part E – Execution Flow

```
┌─────────────┐    push     ┌──────────┐  webhook  ┌─────────┐
│  Developer  │────────────▶│  GitHub  │──────────▶│ Jenkins │
└─────────────┘             └──────────┘           └────┬────┘
                                                        │
                     ┌──────────────────────────────────┘
                     ▼
              ┌─────────────────────────────────────┐
              │         Jenkins Pipeline             │
              │                                     │
              │  Stage 1: Clone  ──▶ Pull from Git  │
              │  Stage 2: Build  ──▶ docker build   │
              │  Stage 3: Auth   ──▶ docker login   │
              │  Stage 4: Push   ──▶ docker push    │
              └──────────────────┬──────────────────┘
                                 │
                                 ▼
                        ┌─────────────────┐
                        │   Docker Hub    │
                        │  (Image Ready)  │
                        └─────────────────┘
```

| Stage | Action |
|---|---|
| **Clone** | Pulls latest code from GitHub |
| **Build** | Docker builds image using `Dockerfile` |
| **Auth** | Jenkins logs into Docker Hub using stored token |
| **Push** | Image pushed to Docker Hub and available globally |

---

## Understanding Jenkinsfile Syntax

### Basic Structure

```groovy
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                sh 'echo Hello'
            }
        }
    }
}
```

### Key Terms

| Block / Keyword | Purpose |
|---|---|
| `pipeline {}` | Root block — everything lives inside this |
| `agent any` | Run on any available node (host Docker in this case) |
| `stages {}` | Groups all pipeline phases |
| `stage('Name')` | A single phase — visible as a block in Jenkins GUI |
| `steps {}` | Contains the actual commands to execute |
| `sh 'command'` | Runs a Linux shell command |
| `git 'url'` | Clones source code from GitHub |
| `echo "msg"` | Prints a message to the Jenkins console log |

---

### The `withCredentials` Block Explained

This is the trickiest part for beginners. It securely injects stored secrets as temporary environment variables.

```groovy
withCredentials([string(credentialsId: 'dockerhub-token', variable: 'DOCKER_TOKEN')]) {
    sh 'echo $DOCKER_TOKEN | docker login -u your-username --password-stdin'
}
```

#### Breakdown

| Part | Meaning |
|---|---|
| `string` | Type of secret (plain text token) |
| `credentialsId` | The ID the secret was saved under in Jenkins |
| `variable` | Temporary variable name to access the secret |
| `$DOCKER_TOKEN` | Replaced with the actual token at runtime |
| `--password-stdin` | Secure login — no plaintext password in logs |

#### Mental Model

```
Jenkins Credentials Store  (dockerhub-token)
             ↓
  withCredentials injects → DOCKER_TOKEN
             ↓
    sh uses $DOCKER_TOKEN
             ↓
  Secret disappears after block ends
```

#### Common Mistakes

```groovy
// Never hardcode credentials
sh 'docker login -u user -p mypassword'

// Wrong credential ID
credentialsId: 'wrong-name'

// Variable used outside the block — won't work
echo $DOCKER_TOKEN
```

#### Clean Final Example

```groovy
stage('Push to Docker Hub') {
    steps {
        withCredentials([string(credentialsId: 'dockerhub-token', variable: 'DOCKER_TOKEN')]) {
            sh '''
            echo $DOCKER_TOKEN | docker login -u your-username --password-stdin
            docker push your-username/myapp:latest
            '''
        }
    }
}
```

---

## Key Notes

- Jenkins is GUI-based but the pipeline itself is fully code-driven via `Jenkinsfile`
- **Never hardcode secrets** — always use the Jenkins Credentials store
- Webhook makes CI/CD fully automatic — no manual triggers needed
- Mounting `/var/run/docker.sock` lets Jenkins use the host Docker engine directly
- This setup is ideal for learning and small-scale deployments



