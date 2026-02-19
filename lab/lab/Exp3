## Experiment 3: Building and Running Custom Docker Images (Ubuntu & Alpine Based NGINX)
## Subject
Containerization and DevOps Lab
## Dated
7th Feb 2026
## Objective
The main objective of this experiment is to understand how to create custom Docker images using different base images and run containers from them.
This experiment demonstrates how to:
•⁠  ⁠Create custom Docker images using Dockerfile
•⁠  ⁠Use Ubuntu and Alpine as base images
•⁠  ⁠Install NGINX inside custom images
•⁠  ⁠Build Docker images using custom tags
•⁠  ⁠Run containers with port mapping
•⁠  ⁠Compare image size and performance differences
•⁠  ⁠Verify container output through browser

## Requirements
Software Required
•⁠  ⁠Docker Desktop (Installed and Running)
•⁠  ⁠Terminal (macOS/Linux) or Command Prompt (Windows)
•⁠  ⁠Web Browser

## Procedure and Implementation

### Step 1: Verify Docker Installation
To ensure Docker was installed correctly, the following commands were executed:
•⁠  ⁠docker --version
•⁠  ⁠docker info

### Step 2: Pull Official NGINX Image
The official nginx image was pulled from Docker Hub:
•⁠  ⁠docker pull nginx:latest

### Step 3: Tag Image Using SAP Naming Convention
The official image was tagged using SAP ID for identification:
•⁠  ⁠docker tag nginx:latest nginx-500125960-official

### Step 4: Run Official NGINX Container
•⁠  ⁠docker run -d -p 8080:80 --name nginx-500125960-official-container nginx-500125960-official
This allows nginx to be accessed locally through the browser.

### Step 5: Verify Output in Browser
Opened in browser:
•⁠  ⁠http://localhost:8080
The "Welcome to nginx!" page confirmed successful execution.

## Part 2: Custom Ubuntu Based NGINX Image

### Step 6: Create Project Directory
•⁠  ⁠mkdir nginx-500125960
•⁠  ⁠cd nginx-500125960
•⁠  ⁠mkdir ubuntu
•⁠  ⁠cd ubuntu

### Step 7: Create Dockerfile (Ubuntu Base Image)
•⁠  ⁠FROM ubuntu:22.04

RUN apt-get update && \
    apt-get install -y nginx && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]

### Step 8: Build Ubuntu Custom Image
•⁠  ⁠docker build -t nginx-500125960-ubuntu .

### Step 9: Run Ubuntu Container
•⁠  ⁠docker run -d -p 8081:80 --name nginx-500125960-ubuntu-container nginx-500125960-ubuntu

### Step 10: Verify Ubuntu Container Output
•⁠  ⁠http://localhost:8081

## Part 3: Custom Alpine Based NGINX Image

### Step 11: Create Alpine Directory
•⁠  ⁠cd ..
•⁠  ⁠mkdir alpine
•⁠  ⁠cd alpine

### Step 12: Create Dockerfile (Alpine Base Image)
•⁠  ⁠FROM alpine:latest

•⁠  ⁠RUN apk add --no-cache nginx

•⁠  ⁠EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]

### Step 13: Build Alpine Custom Image
•⁠  ⁠docker build -t nginx-500125960-alpine .

### Step 14: Run Alpine Container
•⁠  ⁠docker run -d -p 8082:80 --name nginx-500125960-alpine-container nginx-500125960-alpine

### Step 15: Verify Alpine Container Output
•⁠  ⁠http://localhost:8082

### Step 16: Verify Running Containers
•⁠  ⁠docker ps
All three containers were visible with correct port mapping.

## Screenshots and Output
All screenshots captured during experiment execution are attached below:
•⁠  ⁠Docker Version Check
•⁠  ⁠Pull Official NGINX Image
•⁠  ⁠Dockerfile (Ubuntu)
•⁠  ⁠Docker Build Ubuntu Image
•⁠  ⁠Dockerfile (Alpine)
•⁠  ⁠Docker Build Alpine Image
•⁠  ⁠Container Run Commands
•⁠  ⁠Browser Output for Ports 8080, 8081, 8082
•⁠  ⁠Docker PS Output
