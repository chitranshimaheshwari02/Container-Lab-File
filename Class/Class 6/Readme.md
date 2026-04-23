# Docker Advanced Networking

> Explore advanced Docker network drivers — **Overlay** networks for cross-host container communication in Swarm mode, **Macvlan** for assigning real MAC addresses, and **IPvlan** for IP-level isolation. Includes container ping tests and network inspection.

---

## What You Will Learn

- Initialize Docker Swarm
- Create an attachable overlay network
- Run containers on an overlay network and ping between them by name
- Inspect overlay network details (subnet, peers, containers)
- Check host network interfaces with `ifconfig`
- Create and use Macvlan and IPvlan networks
- Understand errors on macOS/Docker Desktop limitations

---

## Prerequisites

- [Docker Desktop](https://www.docker.com/products/docker-desktop/) installed and running
- Basic familiarity with the terminal

---

## Part 1 — Docker Swarm & Overlay Network

### Step 1 — Initialize Docker Swarm

```bash
docker swarm init --advertise-addr 127.0.0.1
```

**Output (if already active):**

```
Error response from daemon: This node is already part of a swarm.
Use "docker swarm leave" to leave this swarm and join another one.
```

Verify Swarm is active:

```bash
docker info | grep Swarm
```

**Output:**

```
Swarm: active
```

> Overlay networks require Swarm mode to be active. The `--advertise-addr` specifies the address other nodes use to reach this manager.

---

### Step 2 — Create an attachable overlay network

```bash
docker network create -d overlay --attachable my_overlay
```

**Output:**

```
ff8f7m8yestgaujbfq50a4e3s
```

> The `--attachable` flag allows standalone containers (not just Swarm services) to connect to the overlay network.

---

### Step 3 — Verify the network was created

```bash
docker network ls
```

**Output (relevant entries):**

```
NETWORK ID     NAME         DRIVER    SCOPE
ff8f7m8yestg   my_overlay   overlay   swarm
q8f2417320ub   my-overlay   overlay   swarm
su0m3twqig5b   ingress      overlay   swarm
```

---

### Step 4 — Run two containers on the overlay network

```bash
docker run -d --network my_overlay --name app1 alpine sleep 3600
```

```bash
docker run -d --network my_overlay --name app2 alpine sleep 3600
```

---

### Step 5 — Verify both containers are running

```bash
docker ps
```

**Output (relevant entries):**

```
CONTAINER ID   IMAGE    COMMAND        STATUS         NAMES
7641ead59013   alpine   "sleep 3600"   Up 5 seconds   app2
e547dc3b994b   alpine   "sleep 3600"   Up 10 seconds  app1
```

---

### Step 6 — Ping app2 from app1 by name

```bash
docker exec -it app1 ping app2
```

**Output:**

```
PING app2 (10.0.2.4): 56 data bytes
64 bytes from 10.0.2.4: seq=0 ttl=64 time=0.759 ms
64 bytes from 10.0.2.4: seq=1 ttl=64 time=0.131 ms
64 bytes from 10.0.2.4: seq=2 ttl=64 time=0.066 ms
...
15 packets transmitted, 15 packets received, 0% packet loss
round-trip min/avg/max = 0.045/0.143/0.759 ms
```

> Containers on the same overlay network communicate by name. The overlay subnet `10.0.2.0/24` is used automatically.

---

### Step 7 — Inspect the overlay network

```bash
docker network inspect my_overlay
```

**Key details from output:**

```json
{
  "Name": "my_overlay",
  "Driver": "overlay",
  "Scope": "swarm",
  "Attachable": true,
  "IPAM": {
    "Config": [
      {
        "Subnet": "10.0.2.0/24",
        "Gateway": "10.0.2.1"
      }
    ]
  },
  "Containers": {
    "app1": { "IPv4Address": "10.0.2.2/24" },
    "app2": { "IPv4Address": "10.0.2.4/24" }
  },
  "Peers": [
    { "Name": "ec1bd6e432ce", "IP": "192.168.65.3" }
  ]
}
```

---

## Part 2 — Macvlan Network

A **Macvlan** network assigns each container its own MAC address, making it appear as a physical device on your network. It requires specifying a parent network interface.

### Step 1 — Check available network interfaces

```bash
ifconfig
```

Look for an active interface such as `en0`, `en1`, or `eth0`:

```
en0: flags=8963<UP,BROADCAST,...> mtu 1500
      ether 36:4b:49:a5:72:80
      status: inactive
```

---

### Step 2 — Create a Macvlan network

```bash
docker network create -d macvlan \
  --subnet=192.168.1.0/24 \
  --gateway=192.168.1.1 \
  -o parent=en0 \
  my_macvlan
```

> **Note on macOS / Docker Desktop:** Macvlan requires a physical network interface. On macOS with Docker Desktop, this may return an error:
>
> ```
> Error response from daemon: invalid subinterface vlan name en0,
> example formatting is eth0.10
> ```
>
> Macvlan works fully on Linux hosts. On macOS, use a Linux VM or Docker on Linux for this feature.

---

### Step 3 — Run a container on the Macvlan network

```bash
docker run -d \
  --name web-macvlan \
  --network my_macvlan \
  nginx
```

> If the Macvlan network creation failed, this will also error:
>
> ```
> docker: Error response from daemon: failed to set up container networking:
> Could not attach to network my_macvlan: rpc error: code = NotFound
> ```

---

## Part 3 — IPvlan Network

**IPvlan** is similar to Macvlan but all containers share the same MAC address — only the IP differs. Useful when the switch limits the number of MAC addresses per port.

### Step 1 — Create an IPvlan network

```bash
docker network create -d ipvlan \
  --subnet=192.168.1.0/24 \
  --gateway=192.168.1.1 \
  -o parent=en0 \
  my_ipvlan
```

---

### Step 2 — Run a container on the IPvlan network

```bash
docker run -d \
  --name web-ipvlan \
  --network my_ipvlan \
  nginx
```

> **Note on macOS / Docker Desktop:** Like Macvlan, IPvlan requires a Linux host with a physical parent interface. On macOS it will return:
>
> ```
> docker: Error response from daemon: failed to set up container networking:
> Could not attach to network my_ipvlan: rpc error: code = NotFound
> ```
>
> These network drivers are **fully supported on Linux** hosts.

---

## 💡 Network Driver Comparison

| Driver | Scope | MAC per Container | Works on macOS | Best For |
|--------|-------|-------------------|----------------|----------|
| `bridge` | local | Shared | Yes | Default single-host setup |
| Custom `bridge` | local | Shared | Yes | Multi-container apps |
| `overlay` | swarm | Shared | Yes (Swarm) | Cross-host container comms |
| `macvlan` | local | Unique per container | Linux only | Appear as physical devices |
| `ipvlan` | local | Shared | Linux only | IP isolation, same MAC |
| `host` | local | Host MAC | Yes | Max performance |
| `none` | local | None | Yes | Full isolation |

---

## Key Commands Reference

```bash
# Initialize Docker Swarm
docker swarm init --advertise-addr 127.0.0.1

# Check Swarm status
docker info | grep Swarm

# Create an attachable overlay network
docker network create -d overlay --attachable <network-name>

# Create a Macvlan network
docker network create -d macvlan \
  --subnet=<subnet> --gateway=<gateway> \
  -o parent=<interface> <network-name>

# Create an IPvlan network
docker network create -d ipvlan \
  --subnet=<subnet> --gateway=<gateway> \
  -o parent=<interface> <network-name>

# Run a container on a specific network
docker run -d --network <network-name> --name <name> <image>

# Ping between containers by name
docker exec -it <container> ping <other-container>

# Inspect a network
docker network inspect <network-name>

# List all networks
docker network ls

# View host network interfaces
ifconfig
```




<img width="1467" height="143" alt="Screenshot 2026-04-23 at 1 33 09 PM" src="https://github.com/user-attachments/assets/86cd1297-be59-40e3-89ba-dc136d87d068" />


<img width="1466" height="196" alt="Screenshot 2026-04-23 at 1 32 57 PM" src="https://github.com/user-attachments/assets/5e4e447c-916b-4df7-8d2c-dbf9816fdb71" />


<img width="1470" height="956" alt="Screenshot 2026-04-23 at 1 32 36 PM" src="https://github.com/user-attachments/assets/527fefc6-42ad-4d6b-8ba9-fd8cbd580b79" />


<img width="1470" height="956" alt="Screenshot 2026-04-23 at 1 32 25 PM" src="https://github.com/user-attachments/assets/dca7cc4b-53a2-4696-a258-1993503df86f" />


<img width="1469" height="295" alt="Screenshot 2026-04-23 at 1 32 13 PM" src="https://github.com/user-attachments/assets/e5c6a000-f682-4c45-9447-81849cdd421e" />


<img width="1470" height="482" alt="Screenshot 2026-04-23 at 1 31 58 PM" src="https://github.com/user-attachments/assets/0e755095-7fa3-4fd7-82f8-8d83aafba7ef" />


<img width="1470" height="49" alt="Screenshot 2026-04-23 at 1 31 46 PM" src="https://github.com/user-attachments/assets/6953c45c-1245-4f8b-bd67-d5187b1a070b" />


<img width="1470" height="279" alt="Screenshot 2026-04-23 at 1 31 31 PM" src="https://github.com/user-attachments/assets/8599e255-c227-4825-b57c-d7f0fd739dcf" />


<img width="1467" height="29" alt="Screenshot 2026-04-23 at 1 31 18 PM" src="https://github.com/user-attachments/assets/523b5484-c951-4c9d-86dc-c455efcb4b9d" />


<img width="1466" height="57" alt="Screenshot 2026-04-23 at 1 31 04 PM" src="https://github.com/user-attachments/assets/66620fde-3292-4fd5-b5bd-aafbec8df0ae" />

















