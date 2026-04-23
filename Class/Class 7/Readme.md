# Docker Compose Demo — Nginx with docker run & Compose

> Learn to run Nginx using `docker run` with various flags (port mapping, volumes, env vars, restart policy, entrypoint), then manage the same setup using **Docker Compose** for a cleaner, reproducible workflow.

---

## Project Structure

```
docker-compose-demo/
├── html/
│   └── index.html          # HTML file served by Nginx
└── docker-compose.yml      # Compose service definition
```

---

## Prerequisites

- [Docker Desktop](https://www.docker.com/products/docker-desktop/) installed and running
- Basic familiarity with the terminal

---

## 📂 Part 1 — Setup

### Step 1 — Create the project folder and HTML file

```bash
mkdir docker-compose-demo
cd docker-compose-demo
mkdir html
nano html/index.html
```

Add content to `index.html`:

```html
<h1>Hello from Docker Compose Demo</h1>
```

---

## Part 2 — Running Nginx with `docker run` Flags

### Step 2 — Run Nginx with port mapping, volume, env var, and restart policy

```bash
docker run \
  --name my-nginx \
  -p 8080:80 \
  -v "$(pwd)/html:/usr/share/nginx/html" \
  -e NGINX_HOST=localhost \
  --restart unless-stopped \
  -d \
  nginx:alpine
```

**Flag breakdown:**

| Flag | Description |
|------|-------------|
| `--name my-nginx` | Name the container `my-nginx` |
| `-p 8080:80` | Map host port `8080` to container port `80` |
| `-v "$(pwd)/html:/usr/share/nginx/html"` | Bind mount local `html/` as the web root |
| `-e NGINX_HOST=localhost` | Set an environment variable inside the container |
| `--restart unless-stopped` | Auto-restart the container unless manually stopped |
| `-d` | Run in detached (background) mode |

> If you see `The container name "/my-nginx" is already in use`, remove it first:
> ```bash
> docker rm -f my-nginx
> ```

---

### Step 3 — Verify the container is running

```bash
docker ps
```

**Output (relevant entry):**

```
CONTAINER ID   IMAGE          STATUS        PORTS                  NAMES
<id>           nginx:alpine   Up X seconds  0.0.0.0:8080->80/tcp   my-nginx
```

---

### Step 4 — Open in browser

Visit [http://localhost:8080](http://localhost:8080)

**Expected output:**

```
Welcome to nginx!
```

---

### Step 5 — Remove the container

```bash
docker rm -f my-nginx
```

---

## Part 3 — Docker Compose

Instead of typing long `docker run` commands, define everything in a `docker-compose.yml` file.

### Step 6 — Write the Compose file

```bash
nano docker-compose.yml
```

Add the following:

```yaml
services:
  my-nginx:
    image: nginx:alpine
    ports:
      - "8080:80"
    volumes:
      - ./html:/usr/share/nginx/html
    environment:
      - NGINX_HOST=localhost
    restart: unless-stopped
```

> Remove the `version:` field if present — it is obsolete in newer versions of Docker Compose and will show a warning.

---

### Step 7 — Start services with Compose

```bash
docker compose up -d
```

**Output:**

```
[+] up 2/2
✔ Network docker-compose-demo_default   Created
✔ Container my-nginx                    Created
```

> If port `8080` is already in use by another container, you will see:
> ```
> Error: failed to bind host port 0.0.0.0:8080/tcp: address already in use
> ```
> Stop the conflicting container first with `docker rm -f <name>`.

---

### Step 8 — Verify with docker ps

```bash
docker ps
```

---

### Step 9 — Stop and remove Compose services

```bash
docker compose down
```

**Output:**

```
[+] down 2/2
✔ Container my-nginx                    Removed
✔ Network docker-compose-demo_default   Removed
```

---

## ⚙️ Part 4 — Additional `docker run` Features

### Run Nginx on port 8081 (foreground, with logs)

```bash
docker run -p 8081:80 nginx
```

> Nginx starts in the foreground and prints logs live. Press `Ctrl+C` to stop.

---

### Run Nginx with only a volume mount (no port mapping)

```bash
docker run -v "$(pwd)/html:/usr/share/nginx/html" nginx
```

---

### Pass an environment variable

```bash
docker run -e MY_VAR=hello nginx
```

---

### Override the default command

```bash
docker run nginx echo "Hello World"
```

**Output:**

```
Hello World
```

> The container starts, runs `echo "Hello World"`, and exits immediately — the default Nginx entrypoint is replaced.

---

### Override the entrypoint

```bash
docker run --entrypoint /bin/bash -it nginx
```

**Output:**

```
root@17e33bcdffffe:/#
```

> Drops you into a Bash shell inside the Nginx container, bypassing the default startup script.

---

## 💡 `docker run` vs Docker Compose

| Feature | `docker run` | Docker Compose |
|---------|-------------|----------------|
| Single container | Quick | Possible |
| Multiple containers | Tedious | Easy |
| Reproducible setup | Manual flags | YAML file |
| Start all services | One command each | `docker compose up -d` |
| Stop all services | One command each | `docker compose down` |
| Shared networks | Manual | Automatic |
| Best for | Quick tests | Dev & production |

---

## Key Commands Reference

```bash
# Run a container with all flags
docker run --name <n> -p <host>:<container> -v <src>:<dst> \
  -e KEY=VALUE --restart unless-stopped -d <image>

# Start Docker Compose services
docker compose up -d

# Stop and remove Docker Compose services
docker compose down

# List running containers
docker ps

# Force remove a container
docker rm -f <container-name>

# Override the entrypoint
docker run --entrypoint /bin/bash -it <image>

# Override the command
docker run <image> echo "Hello World"
```

---




<img width="1466" height="63" alt="Screenshot 2026-04-23 at 4 50 40 PM" src="https://github.com/user-attachments/assets/31be622f-67a6-4a85-be95-c61ed5136d60" />


<img width="1470" height="956" alt="Screenshot 2026-04-23 at 4 50 26 PM" src="https://github.com/user-attachments/assets/baf8eca5-eaf3-472d-ba55-31f34eb21be3" />


<img width="1470" height="956" alt="Screenshot 2026-04-23 at 4 50 12 PM" src="https://github.com/user-attachments/assets/f0f3b4c8-ed6c-471b-8177-223b8bc73008" />


<img width="1470" height="956" alt="Screenshot 2026-04-23 at 4 49 57 PM" src="https://github.com/user-attachments/assets/0df7ff6f-f877-4914-9671-36fd691075ec" />


<img width="1470" height="71" alt="Screenshot 2026-04-23 at 4 49 42 PM" src="https://github.com/user-attachments/assets/7d4e36da-6c31-4753-95ff-c83ecbb4a2e8" />


<img width="1469" height="543" alt="Screenshot 2026-04-23 at 4 49 25 PM" src="https://github.com/user-attachments/assets/5561f975-0a52-4c61-8640-27513b80a209" />


<img width="1470" height="30" alt="Screenshot 2026-04-23 at 4 46 55 PM" src="https://github.com/user-attachments/assets/e99daf46-e342-4e71-982b-3c343ed95c7a" />


<img width="1470" height="879" alt="Screenshot 2026-04-23 at 4 45 07 PM" src="https://github.com/user-attachments/assets/4558ecae-a483-4963-8967-eaa546154d73" />


<img width="1467" height="431" alt="Screenshot 2026-04-23 at 4 44 51 PM" src="https://github.com/user-attachments/assets/61dc4c78-3809-4d04-9448-afe897386a47" />


<img width="1470" height="168" alt="Screenshot 2026-04-23 at 4 44 36 PM" src="https://github.com/user-attachments/assets/072d54fd-a96f-4c17-ba6f-36ba8910d177" />


<img width="1470" height="56" alt="Screenshot 2026-04-23 at 4 44 20 PM" src="https://github.com/user-attachments/assets/3a0d12e5-aac1-42a2-92aa-4fdcd53743f7" />













