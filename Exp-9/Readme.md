# EXPERIMENT 9 - Ansible 

A step-by-step guide to installing Ansible via Homebrew on macOS, generating SSH keys, and using Docker to create an Ubuntu server for Ansible-managed SSH connections.

## 1. Install Ansible

```bash
# Update Homebrew packages
brew update

# Install Ansible
brew install ansible

# Verify installation
ansible --version
```

**Expected output:**
```
ansible [core 2.20.4]
  config file = None
  configured module search path = ['/Users/<user>/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /opt/homebrew/Cellar/ansible/13.5.0_1/libexec/lib/python3.14/site-packages/ansible
  ansible collection location = /Users/<user>/.ansible/collections:/usr/share/ansible/collections
  executable location = /opt/homebrew/bin/ansible
  python version = 3.14.4
  jinja version = 3.1.6
  pyyaml version = 6.0.3 (with libyaml v0.2.5)
```

> **Note:** If `pip install ansible` fails with `command not found: pip`, use `brew install ansible` instead.

---

## 2. Test Ansible on Localhost

```bash
ansible localhost -m ping
```

**Expected output:**
```json
localhost | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

> **Warning:** `[WARNING]: No inventory was parsed, only implicit localhost is available` is normal when no inventory file is configured.

---

## 3. Generate an SSH Key Pair

```bash
ssh-keygen -t rsa -b 4096
```

Accept the default file path (`~/.ssh/id_rsa`) and optionally set a passphrase.

**Example output:**
```
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/<user>/.ssh/id_rsa):
Your identification has been saved in /Users/<user>/.ssh/id_rsa
Your public key has been saved in /Users/<user>/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:hXJ2ZnsuGmyezbfN359tuYbtd8XpDmex5N/Ba9xok+A <user>@Chitranshis-MacBook-Air.local
```

---

## 4. Build the Docker Ubuntu SSH Server

### 4.1 Project Structure

```
ssh-docker/
├── Dockerfile
└── id_rsa.pub       ← copied from ~/.ssh/id_rsa.pub
```

Copy your public key into the project directory:
```bash
cp ~/.ssh/id_rsa.pub ./ssh-docker/
```

### 4.2 Dockerfile

```dockerfile
FROM ubuntu:latest

RUN apt update -y && apt install -y openssh-server && mkdir -p /var/run/sshd

RUN echo 'root:password' | chpasswd

RUN sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config && \
    sed -i 's/#PasswordAuthentication yes/PasswordAuthentication no/' /etc/ssh/sshd_config

RUN mkdir -p /root/.ssh && chmod 700 /root/.ssh

COPY id_rsa.pub /root/.ssh/authorized_keys
RUN chmod 600 /root/.ssh/authorized_keys

RUN sed -i 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' /etc/pam.d/sshd

EXPOSE 22
CMD ["/usr/sbin/sshd", "-D"]
```

### 4.3 Build the Image

```bash
cd ssh-docker
docker build -t ubuntu-server .
```

---

## 5. Run the Container

```bash
docker run -d -p 2222:22 --name ssh-test-server ubuntu-server
```

Inspect the container's internal IP:
```bash
docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' ssh-test-server
# e.g. 172.17.0.5
```

---

## 6. Connect via SSH

```bash
ssh root@localhost -p 2222
```

When prompted about the host fingerprint, type `yes` to add it to known hosts.

**Expected:**
```
Welcome to Ubuntu 24.04.4 LTS (GNU/Linux 6.12.68-linuxkit aarch64)
root@<container_id>:~#
```

Verify the OS inside the container:
```bash
cat /etc/os-release
# PRETTY_NAME="Ubuntu 24.04.4 LTS"
```

---

## 7. Install nginx Inside the Container (Optional Test)

```bash
apt update
apt install -y nginx
```

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| `pip: command not found` | Use `brew install ansible` instead |
| `ansible 13.5.0_1 is already installed` | Run `brew reinstall ansible` if needed |
| `ssh: connect to host 172.17.0.5 port 22: Operation timed out` | Use `localhost -p 2222` instead of the Docker internal IP from macOS host |
| Host key verification failed | Remove the old entry: `ssh-keygen -R [localhost]:2222` |

---

## Summary

| Step | Command |
|------|---------|
| Install Ansible | `brew install ansible` |
| Test Ansible | `ansible localhost -m ping` |
| Generate SSH key | `ssh-keygen -t rsa -b 4096` |
| Build Docker image | `docker build -t ubuntu-server .` |
| Run container | `docker run -d -p 2222:22 --name ssh-test-server ubuntu-server` |
| SSH into container | `ssh root@localhost -p 2222` |
