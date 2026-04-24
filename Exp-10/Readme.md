# Experiment 10: SonarQube — Continuous Code Quality Inspection

> Automatically detect bugs, vulnerabilities, and code smells with every commit using SonarQube's static analysis platform.

---

## Table of Contents

- [Theory](#-theory)
- [Key Concepts](#-key-concepts)
- [Lab Architecture](#-lab-architecture)
- [Setup Guide](#-setup-guide)
  - [Step 1: Setup SonarQube Environment](#step-1-setup-sonarqube-environment)
  - [Step 2: Create Sample Application](#step-2-create-sample-application-with-code-issues)
  - [Step 3: Install SonarQube Scanner](#step-3-install-sonarqube-scanner)
  - [Step 4: Configure and Run Analysis](#step-4-configure-and-run-sonarqube-analysis)
  - [Step 5: Analyze Results](#step-5-analyze-results)
- [Tool Comparison](#-tool-comparison-matrix)
- [Best Practices](#-best-practices)
- [Quick Reference](#-quick-reference-commands)

---

## Theory

### Problem Statement

Code quality issues — bugs, vulnerabilities, and code smells — are often discovered **late in the development cycle**, making them expensive to fix. Manual code reviews are inconsistent and don't scale.

### What is SonarQube?

SonarQube is an **open-source platform for continuous inspection of code quality**. It performs automatic reviews with static analysis to detect bugs, code smells, and security vulnerabilities.

### How SonarQube Solves the Problem

| Feature | Description |
|---|---|
| **Continuous Inspection** | Scans code with every commit, providing immediate feedback |
| **Quality Gates** | Defines pass/fail criteria before deployment |
| **Technical Debt Quantification** | Measures effort needed to fix all issues |
| **Multi-language Support** | Supports 20+ programming languages |
| **Visual Analytics** | Dashboard showing code quality metrics and trends |

---

## Key Concepts

| Term | Definition |
|---|---|
| **Quality Gate** | Set of conditions that code must meet before deployment |
| **Technical Debt** | Estimated time to fix all code issues |
| **Code Smells** | Maintainability issues that don't affect functionality |
| **Vulnerabilities** | Security-related issues in code |
| **Bugs** | Code that might break or behave unexpectedly |
| **Coverage** | Percentage of code covered by tests |
| **Duplications** | Repeated code blocks across the codebase |

---

## Lab Architecture

```
┌─────────────────┐     HTTP      ┌──────────────────┐
│  Developer      │──────────────▶│  SonarQube       │
│  Machine        │               │  Server          │
│                 │               │  (Container)     │
└─────────────────┘               └──────────────────┘
        │                                │
        ▼                                ▼
┌─────────────────┐               ┌──────────────────┐
│  Application    │               │  PostgreSQL      │
│  Source Code    │               │  Database        │
│  (Java/Python)  │               │  (Container)     │
└─────────────────┘               └──────────────────┘
```

---

## ⚙️ Setup Guide

### Step 1: Setup SonarQube Environment

You can set up SonarQube using **Docker CLI** or **Docker Compose**.

#### Option A: Docker CLI

```bash
# Create Docker network
docker network create sonarqube-lab

# Start PostgreSQL database for SonarQube
docker run -d \
  --name sonar-db \
  --network sonarqube-lab \
  -e POSTGRES_USER=sonar \
  -e POSTGRES_PASSWORD=sonar \
  -e POSTGRES_DB=sonarqube \
  -v sonar-db-data:/var/lib/postgresql/data \
  postgres:13

# Start SonarQube server
docker run -d \
  --name sonarqube \
  --network sonarqube-lab \
  -p 9000:9000 \
  -e SONAR_JDBC_URL=jdbc:postgresql://sonar-db:5432/sonarqube \
  -e SONAR_JDBC_USERNAME=sonar \
  -e SONAR_JDBC_PASSWORD=sonar \
  -v sonar-data:/opt/sonarqube/data \
  -v sonar-extensions:/opt/sonarqube/extensions \
  sonarqube:lts-community

# Wait for SonarQube to start (~2-3 minutes)
docker logs -f sonarqube
```

> Access SonarQube at **http://localhost:9000**  
> Default credentials: `admin` / `admin`

#### Option B: Docker Compose

Create a `docker-compose.yml` file:

```yaml
version: '3.8'

services:
  sonar-db:
    image: postgres:13
    container_name: sonar-db
    restart: unless-stopped
    environment:
      POSTGRES_USER: sonar
      POSTGRES_PASSWORD: sonar
      POSTGRES_DB: sonarqube
    volumes:
      - sonar-db-data:/var/lib/postgresql/data
    networks:
      - sonarqube-lab

  sonarqube:
    image: sonarqube:lts-community
    container_name: sonarqube
    restart: unless-stopped
    ports:
      - "9000:9000"
    environment:
      SONAR_JDBC_URL: jdbc:postgresql://sonar-db:5432/sonarqube
      SONAR_JDBC_USERNAME: sonar
      SONAR_JDBC_PASSWORD: sonar
    volumes:
      - sonar-data:/opt/sonarqube/data
      - sonar-extensions:/opt/sonarqube/extensions
    depends_on:
      - sonar-db
    networks:
      - sonarqube-lab

volumes:
  sonar-db-data:
  sonar-data:
  sonar-extensions:

networks:
  sonarqube-lab:
    driver: bridge
```

Then run:

```bash
docker-compose up -d
```

---

### Step 2: Create Sample Application with Code Issues

```bash
# Install Java
sudo apt update
sudo apt install openjdk-17-jdk -y
javac -version

# Create project structure
mkdir -p sample-java-app/src/main/java/com/example
```

Create `sample-java-app/src/main/java/com/example/Calculator.java`:

```java
package com.example;

import java.util.ArrayList;
import java.util.List;

public class Calculator {

    // Bug: Division by zero not handled
    public int divide(int a, int b) {
        return a / b;
    }

    // Code Smell: Unused variable
    public int add(int a, int b) {
        int result = a + b;
        int unused = 100;  // unused variable
        return result;
    }

    // Vulnerability: SQL injection risk
    public String getUser(String userId) {
        String query = "SELECT * FROM users WHERE id = " + userId;
        return query;
    }

    // Duplicate code
    public int multiply(int a, int b) {
        int result = 0;
        for (int i = 0; i < b; i++) {
            result = result + a;
        }
        return result;
    }

    // Duplicate code (same as multiply)
    public int multiplyAlt(int a, int b) {
        int result = 0;
        for (int i = 0; i < b; i++) {
            result = result + a;
        }
        return result;
    }

    // Code Smell: Too many parameters
    public void processUser(String name, String email, String phone,
                           String address, String city, String state,
                           String zip, String country) {
        System.out.println("Processing: " + name);
    }

    // Bug: Null pointer risk
    public String getName(String name) {
        return name.toUpperCase();  // NullPointerException if name is null
    }

    // Code Smell: Empty catch block
    public void riskyOperation() {
        try {
            int x = 10 / 0;
        } catch (Exception e) {
            // swallowing exception silently
        }
    }
}
```

Create `sample-java-app/pom.xml`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
         http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>sample-app</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>11</maven.compiler.source>
        <maven.compiler.target>11</maven.compiler.target>
        <sonar.projectKey>sample-java-app</sonar.projectKey>
        <sonar.host.url>http://localhost:9000</sonar.host.url>
        <sonar.login>your-sonar-token</sonar.login>
    </properties>

    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.13.2</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.sonarsource.scanner.maven</groupId>
                <artifactId>sonar-maven-plugin</artifactId>
                <version>3.9.1.2184</version>
            </plugin>
        </plugins>
    </build>
</project>
```

---

### Step 3: Install SonarQube Scanner

#### Option A: Using Docker

```bash
docker run -d \
  --name sonar-scanner \
  --network sonarqube-lab \
  -v $(pwd)/sample-java-app:/usr/src \
  sonarsource/sonar-scanner-cli:latest \
  sleep infinity
```

#### Option B: Install Locally (Ubuntu/Debian)

```bash
wget https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-5.0.1.3006-linux.zip
unzip sonar-scanner-cli-5.0.1.3006-linux.zip
sudo mv sonar-scanner-5.0.1.3006-linux /opt/sonar-scanner
export PATH=$PATH:/opt/sonar-scanner/bin
```

---

### Step 4: Configure and Run SonarQube Analysis

#### Generate a SonarQube Token

1. Go to **http://localhost:9000** and log in
2. Click **user icon → My Account → Security**
3. Under **Generate Tokens**, create a new token
4. Copy the token (e.g., `sqp_xxxxxxxxxxxx`)

#### Create `sonar-project.properties`

```bash
cat > sample-java-app/sonar-project.properties << 'EOF'
sonar.projectKey=sample-java-app
sonar.projectName=Sample Java Application
sonar.projectVersion=1.0
sonar.sources=src
sonar.java.binaries=target/classes
sonar.language=java
sonar.sourceEncoding=UTF-8
EOF
```

#### Compile and Run Scan

```bash
cd sample-java-app

# Compile the Java source
javac -d target/classes src/main/java/com/example/Calculator.java

# Run scan via Docker
docker exec -e SONAR_TOKEN="<your-token>" \
  sonar-scanner \
  sonar-scanner \
  -Dsonar.host.url=http://sonarqube:9000 \
  -Dsonar.projectKey=sample-java-app \
  -Dsonar.projectName="Sample Java App" \
  -Dsonar.sources=/usr/src/src

# Or run scan locally
sonar-scanner \
  -Dsonar.host.url=http://localhost:9000 \
  -Dsonar.login="<your-token>"
```

---

### Step 5: Analyze Results

Open the dashboard at:

```
http://localhost:9000/dashboard?id=sample-java-app
```

#### Expected Results

| Metric | Count |
|---|---|
| Bugs | 8 |
| Vulnerabilities | 1 |
| Code Smells | 12 |
| Duplicated Blocks | 2 |
| Test Coverage | 0% |
| Technical Debt | ~2 hours |

#### Fetch Issues via API

```bash
curl -u admin:admin \
  "http://localhost:9000/api/issues/search?projectKeys=sample-java-app&types=BUG"
```

---

## Tool Comparison Matrix

| Feature | Jenkins | Ansible | Chef | SonarQube |
|---|---|---|---|---|
| **Primary Purpose** | CI/CD Automation | Config Management | Config Management | Code Quality |
| **Architecture** | Master-Agent | Push, Agentless | Pull, Client-Server | Client-Server |
| **Language** | Java, Groovy | Python, YAML | Ruby | Java |
| **Learning Curve** | Moderate | Low | High | Low |
| **Setup Complexity** | Moderate | Simple | Complex | Simple |
| **Scalability** | High | Very High | Very High | Moderate |
| **Idempotency** | No | Yes | Yes | N/A |

### When to Use What

- **Jenkins** — Build pipelines, testing, and deployment orchestration
- **Ansible** — Simple to medium infrastructure-as-code; agentless environments
- **Chef** — Large-scale enterprise configuration management
- **SonarQube** — Every pull request and nightly builds for code quality gates

---

## Best Practices

### Security
- Never hardcode credentials — use a secrets manager
- Use SSL/TLS for all SonarQube communications
- Apply the principle of least privilege

### Code Quality
- Set Quality Gates to prevent technical debt accumulation
- Enforce code coverage thresholds (**80%+**)
- Run SonarQube scans on every PR in the CI pipeline

### CI/CD Integration

```groovy
pipeline {
    stages {
        stage('Build')     { steps { sh 'mvn clean package' } }
        stage('Test')      { steps { sh 'mvn test' } }
        stage('SonarQube') { steps { sh 'mvn sonar:sonar' } }
        stage('Deploy')    { steps { sh './deploy.sh' } }
    }
}
```

---






<img width="1470" height="351" alt="Screenshot 2026-04-18 at 10 03 54 AM" src="https://github.com/user-attachments/assets/25451f95-20ca-4aca-aa00-b0da1aa1b052" />
<img width="1470" height="674" alt="Screenshot 2026-04-18 at 10 08 56 AM" src="https://github.com/user-attachments/assets/fb2bfa3d-0d58-408e-af43-0b77a1e5feee" />
<img width="1470" height="956" alt="Screenshot 2026-04-18 at 10 09 17 AM" src="https://github.com/user-attachments/assets/0fce8be5-2d00-422e-9255-f117077c5708" />
<img width="1470" height="956" alt="Screenshot 2026-04-18 at 10 09 37 AM" src="https://github.com/user-attachments/assets/0a6846e5-351f-4089-a4f6-e9a582d89232" />
<img width="1470" height="956" alt="Screenshot 2026-04-18 at 10 09 53 AM" src="https://github.com/user-attachments/assets/7a2b4ca2-1bc3-496c-8a5d-917c9728216f" />
<img width="1470" height="342" alt="Screenshot 2026-04-18 at 10 10 22 AM" src="https://github.com/user-attachments/assets/1c28fe1b-4ff3-4837-9059-bfd4f0623e8f" />
<img width="1470" height="108" alt="Screenshot 2026-04-18 at 10 10 45 AM" src="https://github.com/user-attachments/assets/7b98ca40-31d9-47e8-b8fe-af53c3bb46c5" />
<img width="1470" height="880" alt="Screenshot 2026-04-18 at 10 11 31 AM" src="https://github.com/user-attachments/assets/3df54e80-131c-4522-bb8c-b763559396a0" />
<img width="1470" height="825" alt="Screenshot 2026-04-18 at 10 12 11 AM" src="https://github.com/user-attachments/assets/3f812050-7a49-4c46-b289-b9bb32be559a" />
<img width="1470" height="956" alt="Screenshot 2026-04-18 at 10 13 14 AM" src="https://github.com/user-attachments/assets/524debae-09ef-41ff-937a-61db362748e6" />
<img width="1470" height="956" alt="Screenshot 2026-04-18 at 10 13 22 AM" src="https://github.com/user-attachments/assets/ec81bf73-9fa3-41a9-8a6f-c6f25874ea04" />
<img width="1470" height="956" alt="Screenshot 2026-04-18 at 10 13 52 AM" src="https://github.com/user-attachments/assets/52e8ea23-433a-4b68-acab-c54f3e040a26" />
<img width="1470" height="833" alt="Screenshot 2026-04-18 at 10 14 08 AM" src="https://github.com/user-attachments/assets/517ae8c5-f162-453f-acb5-f81d4fb8959d" />
<img width="1470" height="831" alt="Screenshot 2026-04-18 at 10 14 48 AM" src="https://github.com/user-attachments/assets/910bb116-2ee0-4630-9c36-d886234816c6" />
<img width="1470" height="956" alt="Screenshot 2026-04-18 at 10 18 55 AM" src="https://github.com/user-attachments/assets/748a9123-c5d2-46c9-89f8-9f3a4abfb150" />
<img width="1470" height="956" alt="Screenshot 2026-04-18 at 10 19 01 AM" src="https://github.com/user-attachments/assets/1f7fd03e-6af9-404b-8c0d-73a91fe2c258" />
<img width="1469" height="436" alt="Screenshot 2026-04-18 at 10 19 07 AM" src="https://github.com/user-attachments/assets/3724958a-2334-414b-bba5-9dfb04a42ed8" />
<img width="1470" height="956" alt="Screenshot 2026-04-18 at 10 20 15 AM" src="https://github.com/user-attachments/assets/59eb18be-9a4f-4c70-a633-42d5c1767872" />
<img width="1470" height="956" alt="Screenshot 2026-04-18 at 10 20 25 AM" src="https://github.com/user-attachments/assets/eba78d5c-0718-4a92-b61f-abd189e670d8" />
<img width="1470" height="854" alt="Screenshot 2026-04-18 at 10 21 08 AM" src="https://github.com/user-attachments/assets/00b62231-5fef-4457-905b-b767ca9f7d19" />
<img width="1470" height="956" alt="Screenshot 2026-04-18 at 10 23 55 AM" src="https://github.com/user-attachments/assets/1c406fec-a9ea-461e-850e-31f1d5cc5f90" />
<img width="1469" height="791" alt="Screenshot 2026-04-18 at 10 26 31 AM" src="https://github.com/user-attachments/assets/e5743a11-b250-42e6-9f9f-5eda2f79f03b" />
<img width="1465" height="225" alt="Screenshot 2026-04-18 at 10 35 57 AM" src="https://github.com/user-attachments/assets/f91d9b29-ae58-4b27-8fcd-f746506dcce9" />
<img width="1465" height="448" alt="Screenshot 2026-04-18 at 10 36 28 AM" src="https://github.com/user-attachments/assets/1167e8f0-9eee-47d5-b660-f27df71aabb4" />
<img width="1466" height="832" alt="Screenshot 2026-04-18 at 10 36 44 AM" src="https://github.com/user-attachments/assets/2e500eb9-534c-45da-9755-b329e2cb742e" />
<img width="1466" height="240" alt="Screenshot 2026-04-18 at 10 37 03 AM" src="https://github.com/user-attachments/assets/97b152fe-e648-42cf-93f0-f50b810f36e7" />














