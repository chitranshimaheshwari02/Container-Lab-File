# Docker Compose Scaling Demo

> Run and scale multiple services — **Nginx web**, **Redis**, and a **Worker** — using Docker Compose. View live logs from all containers and cleanly tear down the entire stack with one command.

---

## Project Structure

```
docker-scaling-demo/
└── docker-compose.yml      # Multi-service Compose definition
```

---

## Prerequisites

- [Docker Desktop](https://www.docker.com/products/docker-desktop/) installed and running
- Basic familiarity with the terminal

---

## 📄 Part 1 — Docker Compose File

### Step 1 — Create the project folder

```bash
mkdir docker-scaling-demo
cd docker-scaling-demo
```

---

### Step 2 — Write the Compose file

```bash
nano docker-compose.yml
```

Add the following:

```yaml
services:
  web:
    image: nginx
    ports:
      - "8080:80"
    networks:
      - app_network

  redis:
    image: redis
    networks:
      - app_network

  worker:
    image: busybox
    command: sh -c "while true; do echo 'Working...'; sleep 2; done"
    networks:
      - app_network

networks:
  app_network:
    driver: bridge
```

> Remove the `version:` field if present — it is obsolete in newer versions of Docker Compose and will show a warning:
> ```
> WARN[0000] the attribute `version` is obsolete, it will be ignored
> ```

---

## Part 2 — Start All Services

### Step 3 — Start in detached mode

```bash
docker compose up -d
```

**Output:**

```
[+] up 4/4
✔ Network docker-scaling-demo_app_network   Created
✔ Container docker-scaling-demo-web-1       Created
✔ Container docker-scaling-demo-redis-1     Created
✔ Container docker-scaling-demo-worker-1    Created
```

---

### Step 4 — Open Nginx in browser

Visit [http://localhost:8080](http://localhost:8080)

**Expected output:**

```
Welcome to nginx!
```

> Nginx is running and serving on port `8080`, connected to `redis` and `worker` on the shared `app_network`.

---

## 📋 Part 3 — View Live Logs

### Step 5 — Follow logs from all services

```bash
docker compose logs -f
```

**Output (sample):**

```
web-1      | /docker-entrypoint.sh: Configuration complete; ready for start up
web-1      | 2026/04/23 11:30:39 [notice] 1#1: nginx/1.29.5
web-1      | 2026/04/23 11:30:39 [notice] 1#1: start worker processes
web-3      | 2026/04/23 11:30:38 [notice] 1#1: nginx/1.29.5
web-3      | 167.82.56.223 - [23/Apr/2026] "GET / HTTP/1.1" 200 615
worker-1   | Working...
worker-1   | Working...
worker-1   | Working...
```

> Each line is prefixed with the **service name** (`web-1`, `web-3`, `worker-1`) so you can trace which container produced each log entry. Press `Ctrl+C` to stop following.

---

## Part 4 — Stop and Remove All Services

### Step 6 — Tear down the entire stack

```bash
docker compose down
```

**Output:**

```
[+] down 4/4
✔ Container docker-scaling-demo-web-1       Removed    1.2s
✔ Container docker-scaling-demo-redis-1     Removed    0.2s
✔ Container docker-scaling-demo-worker-1    Removed    1.2s
✔ Network docker-scaling-demo_app_network   Removed    0.1s
```

> All containers **and** the shared network are removed in a single command.

---

## Part 5 — WordPress Scaling (Separate Project)

A separate project `wordpress-scaling` uses Docker Compose to run WordPress with a database backend.

### Project folder

```bash
mkdir wordpress-scaling
cd wordpress-scaling
nano docker-compose.yml
```

### Example Compose file

```yaml
services:
  wordpress:
    image: wordpress:latest
    ports:
      - "8080:80"
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: user
      WORDPRESS_DB_PASSWORD: password
      WORDPRESS_DB_NAME: wordpress
    depends_on:
      - db
    networks:
      - wp_network

  db:
    image: mysql:8.0
    environment:
      MYSQL_DATABASE: wordpress
      MYSQL_USER: user
      MYSQL_PASSWORD: password
      MYSQL_ROOT_PASSWORD: rootpassword
    networks:
      - wp_network

networks:
  wp_network:
    driver: bridge
```

### Start WordPress stack

```bash
docker compose up -d
```

### Stop WordPress stack

```bash
docker compose down
```

**Output:**

```
WARN[0000] the attribute `version` is obsolete, it will be ignored
[+] down X/X
✔ All containers and networks removed
```

---

## How Docker Compose Networking Works

All services defined in the same `docker-compose.yml` are automatically placed on a **shared network**. They can reach each other by **service name**:

```
web  ──► redis     (via http://redis:6379)
web  ──► worker    (via http://worker)
```

No IP addresses or manual network creation needed.

---

## Key Docker Compose Concepts

| Concept | Description |
|---------|-------------|
| `services` | Each container definition (web, redis, worker) |
| `networks` | Virtual networks connecting services |
| `depends_on` | Start order — wait for another service first |
| `command` | Override default container command |
| `environment` | Pass env vars into the container |
| `ports` | Map `host:container` port |

---

## Key Commands Reference

```bash
# Start all services in detached mode
docker compose up -d

# Follow live logs from all services
docker compose logs -f

# Follow logs from a specific service
docker compose logs -f web

# Stop and remove all containers + networks
docker compose down

# Stop without removing containers
docker compose stop

# Check running services
docker compose ps

# Scale a service to N replicas
docker compose up -d --scale web=3
```

---



<img width="1470" height="48" alt="Screenshot 2026-04-23 at 5 14 50 PM" src="https://github.com/user-attachments/assets/37f0664b-3071-48aa-bf6b-191e5f7e1059" />


<img width="1467" height="171" alt="Screenshot 2026-04-23 at 5 14 16 PM" src="https://github.com/user-attachments/assets/ff329fce-7c94-4a79-8377-1b41d28ca38b" />


<img width="1470" height="876" alt="Screenshot 2026-04-23 at 5 13 35 PM" src="https://github.com/user-attachments/assets/2100072b-44fd-453c-a4ef-e20d95c54b50" />


<img width="1470" height="875" alt="Screenshot 2026-04-23 at 5 13 17 PM" src="https://github.com/user-attachments/assets/01cf9b56-d913-4601-aa0c-1f44c92ebbf6" />


<img width="1470" height="119" alt="Screenshot 2026-04-23 at 5 11 55 PM" src="https://github.com/user-attachments/assets/f35f3687-9571-45e0-9870-74ae9d722cd1" />


<img width="1470" height="126" alt="Screenshot 2026-04-23 at 5 11 43 PM" src="https://github.com/user-attachments/assets/bba37a3e-f683-4893-b1e7-6b93dfc00f38" />


<img width="1470" height="956" alt="Screenshot 2026-04-23 at 5 11 24 PM" src="https://github.com/user-attachments/assets/95a54e1c-f895-4f35-9302-593d154d8a61" />


<img width="1464" height="112" alt="Screenshot 2026-04-23 at 5 11 10 PM" src="https://github.com/user-attachments/assets/e4ab1188-e2ac-4181-b0a8-3321349d7324" />


<img width="1470" height="190" alt="Screenshot 2026-04-23 at 5 10 53 PM" src="https://github.com/user-attachments/assets/399aa9f7-a541-4eb3-a71f-2750e3dd7163" />
















