Experiment 1: VM vs Containers using Ubuntu & Nginx

## Objective
The objective of this experiment is to understand, implement, and compare:
•⁠  ⁠Virtual Machines using VirtualBox and Vagrant
•⁠  ⁠Containers using Docker
by deploying an Nginx web server in both environments and observing resource utilization.

---

## System Requirements
### Hardware
•⁠  ⁠64-bit system with virtualization enabled
•⁠  ⁠Minimum 4 GB RAM (8 GB recommended)
•⁠  ⁠Internet connection

### Software
•⁠  ⁠Windows OS
•⁠  ⁠Oracle VirtualBox
•⁠  ⁠Vagrant
•⁠  ⁠Ubuntu (Vagrant box)
•⁠  ⁠Docker Engine

---

## Part A: Virtual Machine Setup using Vagrant

### Step 1: Verify Vagrant Installation

vagrant --version


### Step 2: Create Project Directory

mkdir vm-lab
cd vm-lab


### Step 3: Initialize Vagrant with Ubuntu Box

vagrant init ubuntu/jammy64


### Step 4: Start the Virtual Machine

vagrant up


### Step 5: Access the VM via SSH

vagrant ssh


---

## Install Nginx inside VM

sudo apt update
sudo apt install -y nginx
sudo systemctl start nginx


### Verify Nginx

curl localhost


---

## Resource Observation (VM)

free -h
htop
systemd-analyze


---

## Part B: Container Setup using Docker (inside VM)

### Step 1: Install Docker

sudo apt update
sudo apt install -y docker.io
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker vagrant


Re-login to apply permissions:

exit
vagrant ssh


### Verify Docker

docker --version


---

## Run Nginx Container

docker run -d -p 8080:80 --name nginx-container nginx


### Verify Nginx Container

curl localhost:8080


---

## Resource Observation (Container)

docker stats
free -h


---

## 🧹 Cleanup Commands

docker stop nginx-container
docker rm nginx-container
exit
vagrant halt


---

## Comparison Summary

| Parameter | Virtual Machine | Container |
|---------|----------------|-----------|
| Boot Time | High | Very Low |
| RAM Usage | High | Low |
| CPU Overhead | Higher | Minimal |
| Disk Usage | Larger | Smaller |
| Isolation | Strong | Moderate |

---

## Result
This experiment demonstrates that containers are significantly more lightweight and resource-efficient compared to virtual machines. While virtual machines provide stronger isolation and a complete OS-level abstraction, containers are faster, more scalable, and better suited for modern DevOps workflows.

---

## Conclusion
Virtual Machines and Containers both have their place in modern infrastructure. However, containers are preferred in cloud-native and DevOps environments due to their speed, efficiency, and portability.
