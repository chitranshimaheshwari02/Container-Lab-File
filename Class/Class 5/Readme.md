# Docker Networking

> Explore Docker network types — list and inspect networks, connect containers on a custom **bridge network** so they can communicate by name, inspect container network config, and use **host network** mode to share the host's network stack.

---

## 📋 What You Will Learn

- List all Docker networks
- Inspect a network's subnet, gateway, and connected containers
- Create a custom bridge network
- Run containers on the same network and ping between them by name
- Inspect a container's network configuration
- Use `--network host` mode and verify with `lsof`

---

## Prerequisites

- [Docker Desktop](https://www.docker.com/products/docker-desktop/) installed and running
- Basic familiarity with the terminal

---

## Part 1 — List & Inspect Networks

### Step 1 — List all Docker networks

```bash
docker network ls
```

**Output:**

```
NETWORK ID     NAME                              DRIVER    SCOPE
1dd649c93a12   bridge                            bridge    local
e71062b8517b   host                              host      local
su0m3twqig5b   ingress                           overlay   swarm
c405a0938eee   my_bridge                         bridge    local
8161d13fcfa8   my_app_net                        bridge    local
f16a1d13da29   none                              null      local
...
```

> Docker ships with three default networks: `bridge`, `host`, and `none`.

---

### Step 2 — Inspect the default bridge network

```bash
docker network inspect bridge
```

**Key details from output:**

```json
{
  "Name": "bridge",
  "Driver": "bridge",
  "IPAM": {
    "Config": [
      {
        "Subnet": "172.17.0.0/16",
        "Gateway": "172.17.0.1"
      }
    ]
  },
  "Containers": {
    "ssh-test-server": { "IPv4Address": "172.17.0.5/16" },
    "jenkins":         { "IPv4Address": "172.17.0.4/16" },
    "portainer":       { "IPv4Address": "172.17.0.2/16" }
  }
}
```

---

### Step 3 — Inspect a custom bridge network

```bash
docker network inspect my_bridge
```

**Key details from output:**

```json
{
  "Name": "my_bridge",
  "Driver": "bridge",
  "IPAM": {
    "Config": [
      {
        "Subnet": "172.18.0.0/16",
        "Gateway": "172.18.0.1"
      }
    ]
  },
  "Containers": {}
}
```

> Custom bridge networks get their own subnet (e.g., `172.18.0.0/16`) and allow containers to resolve each other **by name**.

---

## Part 2 — Container-to-Container Communication (Custom Bridge)

### Step 1 — Run two containers on the same custom bridge network

```bash
docker run -dit --name container1 --network my_bridge nginx
```

```bash
docker run -dit --name container2 --network my_bridge busybox
```

---

### Step 2 — Verify both containers are running

```bash
docker ps
```

**Output:**

```
CONTAINER ID   IMAGE     COMMAND   STATUS         NAMES
24c8563451cf   busybox   "sh"      Up 4 seconds   container2
b90270246b3e   nginx     "..."     Up 10 seconds  container1
```

---

### Step 3 — Ping container1 from container2 by name

```bash
docker exec -it container2 ping container1
```

**Output:**

```
PING container1 (172.18.0.2): 56 data bytes
64 bytes from 172.18.0.2: seq=0 ttl=64 time=0.436 ms
64 bytes from 172.18.0.2: seq=1 ttl=64 time=0.068 ms
64 bytes from 172.18.0.2: seq=2 ttl=64 time=0.723 ms
...
18 packets transmitted, 18 packets received, 0% packet loss
round-trip min/avg/max = 0.057/0.206/0.723 ms
```

> Containers on the same custom bridge network can reach each other **by container name** — no IP address needed.

---

### Step 4 — Inspect container2's full network config

```bash
docker inspect container2
```

**Key network details from output:**

```json
{
  "NetworkMode": "my_bridge",
  "Networks": {
    "my_bridge": {
      "IPAddress": "172.18.0.3",
      "Gateway":   "172.18.0.1",
      "MacAddress": "..."
    }
  }
}
```

---

## Part 3 — Host Network Mode

In `--network host` mode, the container shares the host machine's network stack directly — no port mapping needed.

### Step 1 — Run Nginx with host networking

```bash
docker run -d --network host nginx
```

---

### Step 2 — Verify with docker ps

```bash
docker ps
```

**Output:**

```
CONTAINER ID   IMAGE   COMMAND                  STATUS        PORTS     NAMES
3db0843c6bca   nginx   "/docker-entrypoint…"    Up seconds              (no port mapping shown)
```

> Notice: no `PORTS` column entry — the container uses the host's port 80 directly.

---

### Step 3 — Verify port 80 is listening on the host

```bash
lsof -i :80
```

**Output:**

```
COMMAND    PID   USER               FD   TYPE  DEVICE SIZE/OFF NODE NAME
com.docke  2305  chitranshimaheshwari  186u  IPv6  ...   0t0  TCP *:http (LISTEN)
```

---

### Step 4 — Open in browser

Visit [http://localhost](http://localhost) in your browser.

**Expected output:**

```
Hello from CI/CD Pipeline
```

> Nginx is accessible directly on `localhost` — no `-p` port mapping flag required when using host network mode.

---

## Network Types Comparison

| Network Type | Isolation | Container DNS | Port Mapping Needed | Best For |
|-------------|-----------|--------------|---------------------|----------|
| `bridge` (default) | Yes | By IP only | Yes | Simple single-host setups |
| Custom `bridge` | Yes | By name | Yes | Multi-container apps |
| `host` | None | N/A | No | Max performance, low-latency |
| `none` | Full | N/A | No | Fully isolated containers |
| `overlay` | Yes | By name | Yes | Docker Swarm multi-host |

---

## Key Commands Reference

```bash
# List all networks
docker network ls

# Inspect a network
docker network inspect <network-name>

# Create a custom bridge network
docker network create <network-name>

# Run a container on a specific network
docker run -dit --name <name> --network <network-name> <image>

# Ping another container by name
docker exec -it <container> ping <other-container>

# Inspect a container's network config
docker inspect <container-name>

# Run a container with host networking
docker run -d --network host <image>

# Check what's listening on a port
lsof -i :<port>
```



<img width="1469" height="874" alt="Screenshot 2026-04-23 at 1 22 47 PM" src="https://github.com/user-attachments/assets/5fa6f6c6-f41e-4dac-9892-5dc513ab21f5" />


<img width="1466" height="43" alt="Screenshot 2026-04-23 at 1 22 33 PM" src="https://github.com/user-attachments/assets/c7df994d-637c-450a-9725-4a79c9691133" />


<img width="1468" height="403" alt="Screenshot 2026-04-23 at 1 22 20 PM" src="https://github.com/user-attachments/assets/ed903fd7-e9fe-4087-b70e-b97553e7e722" />


<img width="1470" height="956" alt="Screenshot 2026-04-23 at 1 22 01 PM" src="https://github.com/user-attachments/assets/5b6b3fcc-3b78-4a36-acba-b3b8ff8a2001" />


<img width="1462" height="337" alt="Screenshot 2026-04-23 at 1 21 45 PM" src="https://github.com/user-attachments/assets/5c853cda-d6a9-41c9-b0e6-0e50bdd7c631" />


<img width="1466" height="375" alt="Screenshot 2026-04-23 at 1 21 29 PM" src="https://github.com/user-attachments/assets/ea49bb91-d696-4190-980b-3ea04fec427e" />


<img width="1470" height="57" alt="Screenshot 2026-04-23 at 1 21 12 PM" src="https://github.com/user-attachments/assets/86389051-82cd-4fef-9b82-e902d8422d6f" />


<img width="1469" height="632" alt="Screenshot 2026-04-23 at 1 20 57 PM" src="https://github.com/user-attachments/assets/e1517f10-1cd9-4916-980a-bad96d79f5ea" />


<img width="1470" height="956" alt="Screenshot 2026-04-23 at 1 20 22 PM" src="https://github.com/user-attachments/assets/8e60c82f-e3ed-4039-b5dd-10ce901f0f9b" />


<img width="1469" height="267" alt="Screenshot 2026-04-23 at 1 20 10 PM" src="https://github.com/user-attachments/assets/9a5c24f7-49a9-44d7-978d-013e47b59115" />






