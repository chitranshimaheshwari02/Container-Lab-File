# Container & DevOps Lab File

> A comprehensive hands-on lab series covering Docker, Kubernetes, CI/CD, Ansible, and Code Quality tooling — from foundational concepts to production-grade pipelines.
> 
## Experiment 1 — Virtual Machines vs Containers

A comparative study of **Virtual Machines** and **Containers** — exploring the fundamental architectural differences between the two approaches to application isolation and deployment.

**What This Does**
- Provisions a VM using Vagrant + VirtualBox and deploys NGINX inside it
- Runs the same NGINX service inside a Docker container
- Compares startup time, resource usage, image/disk size, and isolation model
- Demonstrates why containers are lightweight alternatives to full VMs

**Tech Stack**
```
Ubuntu              Guest OS for VM and container base
VirtualBox          Hypervisor for VM provisioning
Vagrant             VM lifecycle automation (Vagrantfile)
Docker              Container runtime
Nginx               Web server used as the test workload
```

**Key Concepts**
- Hypervisor-based isolation (Type 2) vs OS-level namespace isolation
- VM overhead: full OS kernel, dedicated memory, slow boot
- Container benefits: shared kernel, millisecond startup, minimal footprint
- Infrastructure provisioning via code (Vagrantfile)

**Link:** [Experiment 1 — Virtual Machines vs Containers](./Exp-1/)

---

## Experiment 2 — Docker Installation & Container Lifecycle

An introduction to **Docker fundamentals** — installing Docker, pulling images, running containers, and managing the full container lifecycle through hands-on CLI practice.

**What This Does**
- Installs Docker Engine on the host system
- Pulls official images from Docker Hub (e.g., `nginx`, `ubuntu`)
- Runs containers with port mapping and verifies services via browser
- Detects and resolves port conflict errors
- Practices the full container lifecycle: `create → start → stop → remove`

**Tech Stack**
```
Docker Engine       Container runtime and CLI
Docker Hub          Public image registry
Nginx               Test containerized web service
```

**Key Commands**
```bash
docker pull nginx                        # Pull image from registry
docker run -d -p 8080:80 nginx           # Run with port mapping
docker ps                                # List running containers
docker stop <container_id>               # Stop a container
docker rm <container_id>                 # Remove a container
docker images                            # List local images
```

**Key Concepts**
- Docker image vs container distinction
- Port binding syntax `HOST:CONTAINER`
- Container states: created, running, exited, removed
- Handling `address already in use` port conflicts

 **Link:** [Experiment 2 — Docker Installation & Container Lifecycle](./Exp-2/)

---

## Experiment 3 — Custom Docker Images (Ubuntu & Alpine-Based NGINX)

Demonstrates how to **build custom Docker images** using Dockerfiles with different Linux base distributions, comparing their size, build time, and performance characteristics.

**What This Does**
- Writes Dockerfiles from scratch for Ubuntu 22.04 and Alpine Linux base images
- Installs and configures NGINX inside each custom image
- Builds images with custom tags and runs them on separate ports
- Compares image sizes between Ubuntu-based (~170MB) and Alpine-based (~10MB) builds
- Highlights the advantage of minimal base images for production use

**Tech Stack**
```
Docker              Image build engine and container runtime
Ubuntu 22.04        Full-featured Linux base image
Alpine Linux        Minimal Linux base image (~5MB)
Nginx               Web server installed inside custom images
```

**Port Mapping**
| Container | Base Image | Port |
|-----------|-----------|------|
| Official NGINX | `nginx:latest` | 8080 |
| Custom Ubuntu NGINX | Ubuntu 22.04 | 8081 |
| Custom Alpine NGINX | Alpine Linux | 8082 |

**Key Concepts**
- `FROM`, `RUN`, `COPY`, `EXPOSE`, `CMD` Dockerfile instructions
- Image layering and build cache optimization
- Alpine vs Ubuntu: size vs familiarity tradeoff
- Tagging images: `docker build -t myimage:tag .`

 **Link:** [Experiment 3 — Custom Docker Images (Ubuntu & Alpine Based NGINX)](./Exp-3/)

---

## Experiment 4 — Docker Swarm and Container Orchestration

Demonstrates how to initialize and manage a **Docker Swarm cluster** — Docker's native container orchestration feature — for deploying, scaling, and managing services across nodes.

**What This Does**
- Initializes a Docker Swarm with the current machine as the Manager node
- Generates and uses worker join tokens to simulate a multi-node cluster
- Deploys a service in Swarm mode using `docker service create`
- Scales services across multiple replicas
- Inspects cluster state, node roles, and service distribution

**Tech Stack**
```
Docker Swarm Mode   Native Docker clustering and orchestration
Docker Services     Swarm-managed replicated workloads
Overlay Networks    Multi-host container networking in Swarm
```

**Key Commands**
```bash
docker swarm init                                  # Initialize Swarm, become Manager
docker info                                        # Verify Swarm mode is active
docker swarm join-token worker                     # Get token for worker nodes
docker service create --replicas 3 -p 80:80 nginx  # Deploy a scaled service
docker service ls                                  # List all running services
docker service scale web=5                         # Scale service to 5 replicas
docker node ls                                     # List all nodes in the cluster
```

**Key Concepts**
- Manager vs Worker node responsibilities
- Service vs container: services are the Swarm unit of deployment
- Desired state reconciliation and automatic rescheduling
- Load balancing and high availability across replicas

 **Link:** [Experiment 4 — Docker Essentials Dockerfile Dockerignoring tagging and publishing](./Exp-4/)

---

## Experiment 5 — Docker Volumes, Environment Variables, Monitoring & Network

A deep dive into **Docker storage, configuration, observability, and networking** — covering everything needed to run stateful, configurable, and communicating containers in real-world setups.

**What This Does**
- Creates and mounts Docker named volumes and bind mounts for persistent storage
- Passes environment variables into containers using `--env` and `.env` files
- Monitors container resource consumption (CPU, memory, I/O) using `docker stats`
- Creates custom bridge networks and connects containers for inter-service communication
- Compares default bridge, host, and custom network modes

**Tech Stack**
```
Docker Volumes      Named volumes and bind mounts for persistence
Bind Mounts         Host directory mounted directly into container
Docker Networking   Bridge, Host, and Custom overlay networks
Docker Stats        Real-time resource usage monitoring
Environment Vars    Runtime configuration injection
```

**Key Commands**
```bash
docker volume create mydata                            # Create named volume
docker run -v mydata:/app/data nginx                   # Mount volume into container
docker run -v $(pwd)/html:/usr/share/nginx/html nginx  # Bind mount
docker run -e DB_HOST=localhost -e DB_PORT=5432 myapp  # Pass env vars
docker stats                                           # Live resource usage
docker network create --driver bridge mynet            # Custom network
docker network connect mynet mycontainer               # Attach container to network
```

**Key Concepts**
- Volume vs bind mount: portability and lifecycle differences
- Environment variable injection for 12-factor app design
- Container resource limits and monitoring
- DNS-based container discovery on custom networks

 **Link:** [Experiment 5 — Docker Volumes, Environment Variables, Monitoring Networks Final](./Exp-5/)

---

## Experiment 6 — Docker Run vs Docker Compose

A side-by-side comparison of **imperative `docker run`** commands versus **declarative Docker Compose** YAML configuration — progressing from single containers to multi-service production builds.

**What This Does**
- Maps every `docker run` flag to its Docker Compose YAML equivalent
- Deploys single-container (Nginx) and multi-container (WordPress + MySQL) apps both ways
- Converts given `docker run` commands into equivalent `docker-compose.yml` files
- Builds custom images using the `build:` directive in Compose
- Implements a multi-stage Dockerfile for a production-ready Python FastAPI application

**Tech Stack**
```
Docker               Container runtime and CLI
Docker Compose       Declarative multi-container orchestration
Nginx                Single-container comparison target
MySQL 5.7            Database service in multi-container setup
WordPress            Frontend service in multi-container setup
Node.js 18 Alpine    Custom image build target
Python 3.11 Slim     Multi-stage production build target
```

**Comparison Example**
```bash
# docker run (imperative)
docker run -d --name web -p 8080:80 --network mynet \
  -e ENV=prod -v data:/app nginx

# Equivalent docker-compose.yml (declarative)
services:
  web:
    image: nginx
    ports: ["8080:80"]
    networks: [mynet]
    environment:
      ENV: prod
    volumes:
      - data:/app
```

**Key Concepts**
- Declarative vs imperative infrastructure management
- Multi-stage builds for smaller, secure production images
- Service dependency ordering with `depends_on`
- Shared networks and volumes in Compose

 **Link:** [Experiment 6 — Docker Run vs Docker Compose](./Exp-6)

---

## Experiment 7 — CI/CD using Jenkins, GitHub and Docker Hub

Demonstrates a **complete end-to-end CI/CD pipeline** — automatically building a Dockerized Flask application from GitHub source and pushing the image to Docker Hub on every commit via webhook triggers.

**What This Does**
- Spins up a Jenkins LTS server using Docker Compose with host Docker socket access
- Stores Docker Hub credentials securely in the Jenkins credential store
- Pulls source code from a GitHub repository and builds a Docker image via `Dockerfile`
- Pushes the built image to Docker Hub using a multi-stage `Jenkinsfile` pipeline
- Triggers the entire pipeline automatically on every `git push` via GitHub Webhooks

**Tech Stack**
```
Jenkins LTS          CI/CD automation server
GitHub               Source code and Jenkinsfile repository
Docker               Container runtime and image build engine
Docker Hub           Remote image registry
Python 3.10 Slim     Base image for the Flask application
Flask                Lightweight Python web framework
```

**Pipeline Stages**
```
Checkout → Build Image → Login to Docker Hub → Push Image → Cleanup
```

**Key Concepts**
- Jenkins pipeline as code using `Jenkinsfile`
- GitHub Webhook → Jenkins trigger integration
- Docker-outside-of-Docker (DooD) via socket mounting
- Credential management in Jenkins for secure registry login

 **Link:** [Experiment 7 — CI/CD using Jenkins, GitHub and Docker Hub](./Exp-7)

---

## Experiment 9 — Ansible

Demonstrates **infrastructure provisioning and configuration management** using Ansible on macOS, with a Dockerized Ubuntu SSH server acting as the managed node — a lightweight local lab for practicing automation without physical machines.

**What This Does**
- Installs and configures Ansible on macOS via Homebrew
- Sets up SSH key-based authentication between control node and managed node
- Spins up an Ubuntu 24.04 Docker container with an SSH server as the target host
- Connects to the container via Ansible inventory and runs automated playbooks
- Demonstrates ad-hoc commands and playbook-based configuration management

**Tech Stack**
```
Ansible 2.20.4       Agentless configuration management tool
Docker               Hosts the Ubuntu SSH target container
Ubuntu 24.04 LTS     Managed node operating system
OpenSSH              Secure remote access protocol
```

**Key Commands**
```bash
brew install ansible                               # Install Ansible on macOS
ansible --version                                  # Verify installation
ssh-keygen -t rsa                                  # Generate SSH key pair
ansible all -m ping -i inventory.ini               # Test connectivity
ansible-playbook -i inventory.ini playbook.yml     # Run a playbook
```

**Key Concepts**
- Agentless architecture: Ansible uses SSH, no agent needed on target
- Inventory files: defining managed hosts and groups
- Playbooks: YAML-based automation scripts
- Idempotency: running the same playbook multiple times safely

 **Link:** [Experiment 9 — Ansible](./Exp-9)

---

## Experiment 10 — SonarQube

Demonstrates **continuous code quality inspection** using SonarQube in a Docker-based local lab — scanning a deliberately flawed Java application to detect bugs, vulnerabilities, and code smells through static analysis.

**What This Does**
- Spins up a SonarQube server and PostgreSQL database using Docker Compose
- Sets up a sample Java application with deliberate code issues (bugs, SQL injection, hardcoded credentials)
- Runs the SonarScanner CLI to perform static code analysis on the source
- Visualizes results on the SonarQube dashboard with quality gates and technical debt metrics

**Tech Stack**
```
SonarQube LTS        Continuous code quality inspection server
PostgreSQL 13        Backend database for SonarQube metadata
Docker               Containerized SonarQube + DB environment
Java OpenJDK 17      Language of the sample application
SonarScanner CLI     Static analysis runner
```

**Issue Types Detected**
| Type | Description |
|------|-------------|
| Bug | Division by zero, null pointer dereference |
| Vulnerability | SQL injection, hardcoded credentials |
| Code Smell | Empty methods, magic numbers, poor naming |
| Security Hotspot | Code sections requiring manual security review |

**Key Commands**
```bash
docker compose up -d                               # Start SonarQube + PostgreSQL
# Access dashboard at http://localhost:9000 (admin/admin)
sonar-scanner \
  -Dsonar.projectKey=sample-java-app \
  -Dsonar.sources=src \
  -Dsonar.host.url=http://localhost:9000 \
  -Dsonar.login=<YOUR_TOKEN>
```

**Key Concepts**
- Quality Gates: automated pass/fail thresholds for code health
- Technical Debt: estimated remediation effort displayed as time
- SQALE methodology for maintainability rating (A–E)
- Static analysis vs runtime testing

 **Link:** [Experiment 10 — SonarQube](./Exp-10)

---

## Experiment 11 — WordPress with Docker Compose & Docker Swarm
Demonstrates **container orchestration and service scaling** using Docker Compose and Docker Swarm — deploying a WordPress + MySQL stack locally, then scaling it across replicas using Swarm's orchestration layer.

**What This Does**
- Deploys a WordPress application with a MySQL backend using Docker Compose
- Migrates the stack to Docker Swarm for multi-replica orchestration
- Scales the WordPress service to 3 replicas using a single command
- Manages the full lifecycle: deploy, inspect, scale, and tear down

**Scaling & Stack Commands**
| Task | Command |
|------|---------|
| Start stack | `docker compose up -d` |
| Deploy to Swarm | `docker stack deploy -c docker-compose.yml mystack` |
| Scale replicas | `docker service scale mystack_wordpress=3` |
| Remove stack | `docker stack rm mystack` |

**Key Concepts**
- Docker Compose vs Swarm: local dev vs orchestrated deployment
- Service replicas and task convergence in Swarm mode
- Named volumes for persistent DB and WordPress data
- Port binding and conflict resolution across services

 **Link:** [Experiment 11 — WordPress with Docker Compose & Swarm](./Exp-11)

 ## Experiment 12 — Kubernetes Deployments & Services with k3d
Demonstrates **container orchestration using Kubernetes** — setting up a local cluster with k3d, deploying a containerized Apache (httpd) application as a Pod, and inspecting the full workload lifecycle inside a Kubernetes environment.
 
**What This Does**
- Creates a local Kubernetes cluster using k3d (Kubernetes running inside Docker)
- Verifies cluster health by inspecting nodes and control plane components
- Deploys an Apache (httpd) container as a Kubernetes Pod
- Inspects Pod scheduling, image pulling, and container lifecycle events
 
**Key Concepts**
- k3d as a lightweight local Kubernetes environment running inside Docker
- Kubernetes control plane components: API server, CoreDNS, Metrics Server
- Pod scheduling, node assignment, and container image pulling lifecycle
- Pod conditions: `Initialized`, `PodScheduled`, `ContainerCreating`, `Running`

**Link:** [Experiment 12 — Kubernetes Deployments & Services with k3d](./Exp-12)
 

## Assignment 1 — Containerized Web App with PostgreSQL

A practical assignment deploying a **multi-container web application** backed by PostgreSQL, using Docker Compose for orchestration and Macvlan/IPvlan for advanced network configuration.

**What This Does**
- Deploys a full-stack web application and PostgreSQL database using Docker Compose
- Configures Macvlan/IPvlan networking to assign containers real IP addresses on the host network
- Demonstrates production-like network isolation and container-to-database communication

🔗 **Link:** [Assignment 1 — Containerized Web Application with PostgreSQL](https://github.com/chitranshimaheshwari02/container-project/blob/main/README.md)

---

## Class Assignment — Kubernetes Pod & Deployment

An in-class experiment covering **Kubernetes fundamentals** — creating Pods, writing Deployment manifests, and managing containerized workloads using `kubectl`.

**What This Does**
- Creates and inspects Kubernetes Pods using `kubectl run` and YAML manifests
- Writes Deployment manifests for scalable, self-healing workloads
- Explores Pod lifecycle, ReplicaSets, and rollout strategies

 **Link:** [Class Assignment — Kubernetes Pod & Deployment Experiment](https://github.com/chitranshimaheshwari02/Container-Lab-File/blob/main/Class_assignments/Task)

---



Link : [Class 1](https://github.com/chitranshimaheshwari02/Container-Lab-File/tree/main/Class/Class%201)
Link : [Class 2](https://github.com/chitranshimaheshwari02/Container-Lab-File/tree/main/Class/Class%202)
Link : [Class 3](https://github.com/chitranshimaheshwari02/Container-Lab-File/tree/main/Class/Class%203)
Link : [Class 4](https://github.com/chitranshimaheshwari02/Container-Lab-File/tree/main/Class/Class%204)
Link : [Class 5](https://github.com/chitranshimaheshwari02/Container-Lab-File/tree/main/Class/Class%205)
Link : [Class 6](https://github.com/chitranshimaheshwari02/Container-Lab-File/tree/main/Class/Class%206)
Link : [Class 7](https://github.com/chitranshimaheshwari02/Container-Lab-File/tree/main/Class/Class%207)
Link : [Class 8](https://github.com/chitranshimaheshwari02/Container-Lab-File/tree/main/Class/Class%208)

