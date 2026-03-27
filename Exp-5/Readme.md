## EXPERIMENT 5 - DOCKER VOLUMES, ENVIRONMENT VARIABLES, MONITORING NETWORKS FINAL

A practical walkthrough of Docker storage (volumes, bind mounts) and networking (bridge, host, custom networks), including container stats and environment variable management.

---

## Table of Contents

1. [Container Storage Basics](#1-container-storage-basics)
2. [Docker Volumes](#2-docker-volumes)
3. [Bind Mounts](#3-bind-mounts)
4. [Real-World Example: MySQL Persistence](#4-real-world-example-mysql-persistence)
5. [Nginx with Config Bind Mount](#5-nginx-with-config-bind-mount)
6. [Volume Management](#6-volume-management)
7. [Copying Files into Containers](#7-copying-files-into-containers)
8. [Docker Networking](#8-docker-networking)
9. [Custom Bridge Networks](#9-custom-bridge-networks)
10. [Container Stats & Monitoring](#10-container-stats--monitoring)
11. [Environment Variables](#11-environment-variables)

---

## 1. Container Storage Basics

By default, data written inside a container is **ephemeral** — it disappears when the container is removed.

```bash
# Pull Ubuntu image
docker pull ubuntu

# Run a container and create a file
docker run -it --name test-container ubuntu /bin/bash
mkdir /data
echo "Hello World" > /data/message.txt
cat /data/message.txt   # Output: Hello World
exit

# Remove the container
docker rm test-container

# Spin up a fresh container — the file is gone
docker run -it --name test-container ubuntu /bin/bash
cat /data/message.txt   # Error: No such file or directory
```

**Key takeaway:** Container filesystems do not persist after `docker rm`. Use volumes or bind mounts to persist data.

---

## 2. Docker Volumes

Docker-managed volumes are stored at `/var/lib/docker/volumes/` on the host.

### Anonymous Volume (auto-created)

```bash
docker run -d -v /app/data --name web1 nginx
docker volume ls
docker inspect web1 | grep -A 5 Mounts
```

### Named Volume

```bash
docker volume create mydata
docker run -d -v mydata:/app/data --name web2 nginx

# Inspect the volume
docker volume inspect mydata
```

Example output:
```json
{
  "CreatedAt": "2026-02-28T02:59:13Z",
  "Driver": "local",
  "Mountpoint": "/var/lib/docker/volumes/mydata/_data",
  "Name": "mydata",
  "Scope": "local"
}
```

---

## 3. Bind Mounts

Bind mounts link a **host directory** directly into the container.

```bash
mkdir ~/myapp-data

docker run -d -v ~/myapp-data:/app/data --name web3 nginx

# Write from the host, read from the container
echo "From Host" > ~/myapp-data/host-file.txt
docker exec web3 cat /app/data/host-file.txt   # Output: From Host
```

## 4. Real-World Example: MySQL Persistence

Data survives container replacement when a named volume is used.

```bash
# Start MySQL with a named volume
docker run -d \
  --name mysql-db \
  -v mysql-data:/var/lib/mysql \
  -e MYSQL_ROOT_PASSWORD=secret \
  mysql:8.0

# Stop and remove the container
docker stop mysql-db
docker rm mysql-db

# Recreate — data is still intact
docker run -d \
  --name new-mysql \
  -v mysql-data:/var/lib/mysql \
  -e MYSQL_ROOT_PASSWORD=secret \
  mysql:8.0

docker volume inspect mysql-data
```

---

## 5. Nginx with Config Bind Mount

Mount a custom Nginx config file from the host.

```bash
mkdir ~/nginx-config
echo 'server {
    listen 80;
    server_name localhost;
    location / {
        return 200 "Hello from mounted config!";
    }
}' > ~/nginx-config/nginx.conf

docker run -d \
  --name nginx-custom \
  -p 8080:80 \
  -v ~/nginx-config/nginx.conf:/etc/nginx/conf.d/default.conf \
  nginx

curl http://localhost:8080
```

> **Note:** If port 8080 is already in use, Docker will throw a bind error. Check with `docker ps` and stop the conflicting container first.

---

## 6. Volume Management

```bash
# List all volumes
docker volume ls

# Inspect a specific volume
docker volume inspect <volume-name>

# Remove a specific volume (must not be in use)
docker volume rm <volume-name>

# Remove all unused anonymous volumes
docker volume prune
```

> `docker volume prune` only removes volumes not attached to any container (including stopped ones).

---

## 7. Copying Files into Containers

```bash
# Create a file on the host
echo "Hello from host file" > test.txt

# Copy it into a running container
docker cp test.txt web1:/app/data

# Verify
docker exec web1 cat /app/data/test.txt
```

> **Note:** The target container must be **running**. If it's stopped, start it first with `docker start <name>`.

---

## 8. Docker Networking

### View All Networks

```bash
docker network ls
```

Default networks:

| Name | Driver | Description |
|---|---|---|
| `bridge` | bridge | Default for standalone containers |
| `host` | host | Shares the host's network stack |
| `none` | null | No networking |

### Inspect the Default Bridge

```bash
docker network inspect bridge
```

The default bridge uses subnet `172.17.0.0/16`. Containers on this network can communicate via IP but **not by container name**.

---

## 9. Custom Bridge Networks

Custom bridge networks support **automatic DNS resolution** by container name.

```bash
# Create a custom network
docker network create my-network

# Run containers on it
docker run -d --name web1-new --network my-network nginx
docker run -d --name web2-new --network my-network nginx

# Containers can now reach each other by name
docker exec web1-new ping web2-new
```

### Isolated Network (no external access)

```bash
docker network create --internal isolated-net
docker run -d --name isolated-app --network isolated-net nginx
```

### Host Network (no port mapping needed)

```bash
docker run -d --name host-app --network host nginx
```

---

## 10. Container Stats & Monitoring

### Live stats (streaming)

```bash
docker stats
```

### One-time snapshot

```bash
docker stats --no-stream
```

### Filter columns

```bash
docker stats --no-stream --format "table {{.Name}}\t{{.CPUPerc}}\t{{.MemUsage}}\t{{.NetIO}}"
```

### Custom format (readable)

```bash
docker stats --no-stream --format \
  "Container: {{.Name}} | CPU: {{.CPUPerc}} | Memory: {{.MemPerc}}"
```

### Full container IDs (no truncation)

```bash
docker stats --no-stream --no-trunc
```

### JSON output

```bash
docker stats --format json --no-stream
```

---

## 11. Environment Variables

### Inline environment variables

```bash
docker run -d \
  --name app1 \
  -e DATABASE_URL="postgres://user:pass@db:5432/mydb" \
  -e DEBUG="true" \
  -p 3000:3000 \
  my-node-app
```

### Multiple variables

```bash
docker run -d \
  --name app2 \
  -e VAR1=value1 \
  -e VAR2=value2 \
  -e VAR3=value3 \
  my-app
```

### Using an `.env` file

```bash
# Create the file
echo "DATABASE_HOST=localhost" > .env
echo "DATABASE_PORT=5432" >> .env
echo "API_KEY=secret123" >> .env

# Pass it to the container
docker run -d \
  --name app3 \
  --env-file .env \
  my-app
```

>  Never commit `.env` files containing secrets to version control. Add `.env` to your `.gitignore`.

---

## Quick Reference

```bash
# Volume commands
docker volume create <name>
docker volume ls
docker volume inspect <name>
docker volume rm <name>
docker volume prune

# Network commands
docker network create <name>
docker network ls
docker network inspect <name>
docker network connect <network> <container>
docker network disconnect <network> <container>

# Stats<img width="1470" height="258" alt="Screenshot 2026-02-28 at 8 32 38 AM" src="https://github.com/user-attachments/assets/d5f2bc7c-6a78-49aa-8332-c07a1601ed69" />

docker stats
docker stats --no-stream
docker stats --format "table {{.Name}}\t{{.CPUPerc}}\t{{.MemPerc}}"

# Copy files
docker cp <src> <container>:<dest>
docker cp <container>:<src> <dest>
```


## Screenshots
<img width="1465" height="283" alt="Screenshot 2026-02-28 at 8 31 56 AM" src="https://github.com/user-attachments/assets/f5b97fb5-0561-4fa7-b1a6-59d8a788b5e5" />
<img width="1468" height="236" alt="Screenshot 2026-02-28 at 8 32 24 AM" src="https://github.com/user-attachments/assets/061be17f-9f20-4e74-aa27-f632883e1e22" />
![Uploading Screenshot 2026-02-28 at 8.32.38 AM.png…]()
<img width="1469" height="480" alt="Screenshot 2026-02-28 at 8 32 52 AM" src="https://github.com/user-attachments/assets/231481ac-0739-4bac-81e4-5711a8cc7319" />
<img width="1470" height="650" alt="Screenshot 2026-02-28 at 8 36 55 AM" src="https://github.com/user-attachments/assets/3cf57d43-84de-4969-8d94-d8cb0d6f2cba" />
<img width="1470" height="611" alt="Screenshot 2026-02-28 at 8 37 11 AM" src="https://github.com/user-attachments/assets/e2079eaf-d3da-4585-aac7-2d13e28d90af" />
<img width="1470" height="876" alt="Screenshot 2026-02-28 at 8 42 47 AM" src="https://github.com/user-attachments/assets/92e90fc2-b7ae-450b-a6fc-99b77a4d5b1f" />
<img width="1466" height="886" alt="Screenshot 2026-02-28 at 9 02 08 AM" src="https://github.com/user-attachments/assets/7c5edf69-b3c6-4cf9-9362-aa361505301b" />
<img width="1470" height="664" alt="Screenshot 2026-02-28 at 9 02 24 AM" src="https://github.com/user-attachments/assets/db16062b-85cc-45c7-87c3-18c20879fe5e" />
<img width="1470" height="240" alt="Screenshot 2026-02-28 at 9 07 15 AM" src="https://github.com/user-attachments/assets/6794868b-9707-4461-83ac-95d7f67341fa" />
<img width="1470" height="88" alt="Screenshot 2026-02-28 at 9 07 32 AM" src="https://github.com/user-attachments/assets/e08d0c74-a43e-4d35-bb02-b128ce9ec31a" />
<img width="1470" height="197" alt="Screenshot 2026-02-28 at 9 08 00 AM" src="https://github.com/user-attachments/assets/757971cc-a290-4303-86a2-d01d82f99667" />
<img width="1466" height="212" alt="Screenshot 2026-02-28 at 9 08 23 AM" src="https://github.com/user-attachments/assets/d799bb8f-f59b-4d1c-ae38-9aa5658680be" />
<img width="1470" height="956" alt="Screenshot 2026-02-28 at 9 08 55 AM" src="https://github.com/user-attachments/assets/93e1f987-6fe0-4687-a2cf-82300109ca70" />
<img width="1470" height="183" alt="Screenshot 2026-02-28 at 9 09 24 AM" src="https://github.com/user-attachments/assets/633f92e0-51cb-4a36-ad53-6c1b4881a366" />
<img width="1468" height="390" alt="Screenshot 2026-02-28 at 9 10 31 AM" src="https://github.com/user-attachments/assets/f9f92493-5f2c-43d1-8e7e-f3ffa6d9ebb5" />
<img width="1467" height="231" alt="Screenshot 2026-02-28 at 9 11 30 AM" src="https://github.com/user-attachments/assets/1591606c-e286-4e56-b477-25282252024e" />
<img width="1467" height="432" alt="Screenshot 2026-02-28 at 9 27 28 AM" src="https://github.com/user-attachments/assets/0555dbc4-6815-4ca2-b832-b7e690e85cff" />
