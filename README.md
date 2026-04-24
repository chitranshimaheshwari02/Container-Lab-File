Lab Experiments Included
## Experiment 1 — Virtual Machines vs Containers

This DevOps-focused experiment presents a comparative study of Virtual Machines and Containers using the following technologies:

- Ubuntu

- VirtualBox

- Vagrant

- Docker

- Nginx

### The experiment highlights:

- Infrastructure provisioning techniques

- VM-based deployment processes

- Containerized application execution

- Architectural differences in terms of isolation, resource utilization, and performance

🔗 Link: [Experiment 1 — Virtual Machines vs Containers](./Exp-1/)

## Experiment 2 — Docker Installation & Container Lifecycle

## This experiment introduces the core concepts and practical usage of Docker, including:

- Pulling Docker images from repositories

- Running containers with port mapping

- Accessing and verifying services via a web browser

- Handling and resolving port conflicts

- Managing the container lifecycle (start, stop, remove commands)

🔗 Link: [Experiment 2 — Docker Installation & Container Lifecycle](./Exp-2/)

## Experiment 3 — Custom Docker Images (Ubuntu & Alpine-Based NGINX)

## This experiment demonstrates how to build and deploy custom Docker images using different Linux base distributions while running NGINX inside containers.

- Base Images Used:

- Ubuntu 22.04

- Alpine Linux

- Key learning outcomes include:

- Writing Dockerfiles for custom image creation

- Installing and configuring NGINX inside containers

- Building Docker images with custom tags

- Running multiple containers using different port mappings

- Comparing image size and performance between Ubuntu and Alpine

- Understanding optimization using lightweight container images

- Implementation Details:

Official NGINX container → Port 8080

Ubuntu-based custom container → Port 8081

Alpine-based custom container → Port 8082

🔗 Link: [Experiment 3 — Custom Docker Images (Ubuntu & Alpine Based NGINX)](./Exp-3/)


## Experiment 4 — Docker Swarm and Container Orchestration

This experiment demonstrates how to initialize and manage a Docker Swarm cluster for container orchestration using Docker’s built-in clustering feature.

- Technologies Used:

- Docker Swarm Mode  
- Docker Services  
- Overlay Networks  

- Key learning outcomes include:

- Initializing Docker Swarm  
- Understanding Manager and Worker nodes  
- Generating and using join tokens  
- Deploying services in Swarm mode  
- Scaling services across replicas  
- Inspecting Swarm cluster status  
- Understanding orchestration concepts like load balancing and high availability  

- Implementation Details:

Docker Swarm initialized → `docker swarm init`  
Manager node verification → `docker info`  
Service deployment → `docker service create`  
Scaling services → `docker service scale`  
Service inspection → `docker service ls`  

🔗 Link: [Experiment 4 — Docker Essentials Dockerfile Dockerignoring tagging and plublishing](./Exp-4/)


## Assignment 1

Link: [ Assignment 1 - Containerized Web Application with PostgreSQL using Docker Compose and Macvlan/Ipvlan](https://github.com/chitranshimaheshwari02/container-project/blob/main/README.md)


## Class Assignment
Link : [Class Assignment - Kubernetes Pod & Deployment Experiment] (https://github.com/chitranshimaheshwari02/Container-Lab-File/blob/main/Class_assignments/Task)



## Experiment 5 — Docker Volumes, Environment Variables, Monitoring & Network
This experiment demonstrates how to manage Docker container storage using volumes and bind mounts, configure environment variables, monitor container performance, and work with Docker networking for container communication.

Technologies Used:
Docker Volumes
Bind Mounts
Docker Networking (Bridge, Host, Custom Networks)
Docker Stats (Monitoring)
Environment Variables

Link : [Experiment 5 — Docker Volumes, Environment Variables, Monitoring Networks Final](./Exp-5/)


## Experiment 9 - Ansible 
This experiment demonstrates provisioning and configuration management using Ansible on macOS, combined with a Dockerized Ubuntu SSH server as a managed node — a lightweight local lab for practicing infrastructure automation.

What This Does

Installs and configures Ansible on macOS via Homebrew
Sets up SSH key-based authentication
Spins up an Ubuntu 24.04 container over Docker with an SSH server
Connects to the container via Ansible for automated management

Tech Stack
Ansible 2.20.4       Configuration management
Docker               Containerized Ubuntu SSH target
Ubuntu 24.04 LTS     Managed node OS
Open SSH             Secure remote access

Link : [Experiment 9 - Ansible](./Exp-9)


## Experiment 10 - SonarQube
This experiment demonstrates continuous code quality inspection using SonarQube on a local Docker-based lab — scanning a intentionally flawed Java application to detect bugs, vulnerabilities, and code smells through static analysis.

**What This Does**
- Spins up a SonarQube server and PostgreSQL database using Docker Compose
- Sets up a sample Java application with deliberate code issues
- Runs the SonarQube Scanner to perform static code analysis
- Visualizes results on the SonarQube dashboard with quality gates and technical debt metrics

**Tech Stack**
```
SonarQube LTS        Continuous code quality inspection
PostgreSQL 13        Backend database for SonarQube
Docker               Containerized SonarQube + DB environment
Java (OpenJDK 17)    Language of the sample application
SonarScanner CLI     Static analysis runner
```

**Link :** [Experiment 10 - SonarQube](./Exp-10)
