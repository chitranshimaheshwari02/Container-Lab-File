# Experiment 1: VM vs Containers using Ubuntu & Nginx

## Objective
The objective of this experiment is to understand, implement, and compare:
• Virtual Machines using VirtualBox and Vagrant  
• Containers using Docker  
by deploying an Nginx web server and observing resource utilization.

---

## System Requirements

### Hardware
• 64-bit system with virtualization support  
• Minimum 4 GB RAM (8 GB recommended)  
• Internet connection  

### Software
• macOS  
• Oracle VirtualBox  
• Vagrant  
• Ubuntu Vagrant Box  
• Docker Engine  

---

## Part A: Virtual Machine Setup using Vagrant

### Step 1: Install Homebrew

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
 Containers both have their place in modern infrastructure. However, containers are preferred in cloud-native and DevOps environments due to their speed, efficiency, and portability.

### Step 2: Install VirtualBox
```bash
brew install --cask virtualbox

### Step 3: Install Vagrant
```bash
brew install --cask vagrant

### Step 4: Verify Installation
```bash
vagrant --version
virtualbox --help

### Step 5: Create Project Directory
```bash
mkdir vm-lab
cd vm-lab

### Step 6: Initialize Vagrant with Ubuntu Box
```bash
vagrant init ubuntu/jammy64

### Step 7: Start the Virtual Machine
```bash
vagrant up

### Error Observed
```bash
The box 'ubuntu/jammy64' could not be found or does not support the provider 'virtualbox'.

This box requires the following providers:
    vmware_desktop

Your system supports the following providers:
    virtualbox

