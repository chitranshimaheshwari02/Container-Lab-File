# Docker Nginx — Bind Mount & tmpfs

> Serve a local HTML file with Nginx using a **bind mount**, then explore **tmpfs mounts** for temporary in-memory storage that disappears when the container is removed.

---

## Project Structure

```
html/
└── index.html      # HTML file served by Nginx
```

---

## Prerequisites

- [Docker Desktop](https://www.docker.com/products/docker-desktop/) installed and running
- Basic familiarity with the terminal

---

## Part 1 — Serve HTML with Nginx via Bind Mount

### Step 1 — Create the project folder and HTML file

```bash
mkdir html
cd html
nano index.html
```

Add the following content to `index.html`:

```html
Hello from CI/CD Pipeline
```

Go back to the parent directory:

```bash
cd ..
```

---

### Step 2 — Run Nginx with the HTML folder bind-mounted

```bash
docker run -d \
  --name nginx-container \
  -v "$(pwd)/html:/usr/share/nginx/html" \
  nginx
```

**Flag breakdown:**

| Flag | Description |
|------|-------------|
| `-d` | Run container in detached (background) mode |
| `--name nginx-container` | Name the container `nginx-container` |
| `-v "$(pwd)/html:/usr/share/nginx/html"` | Bind mount local `html/` into Nginx's web root |

---

### Step 3 — Verify the container is running

```bash
docker ps
```

**Output:**

```
CONTAINER ID   IMAGE   COMMAND                  STATUS         PORTS     NAMES
ba4ab4be154b   nginx   "/docker-entrypoint…"    Up 4 seconds   80/tcp    nginx-container
```

---

### Step 4 — Open in browser

Visit [http://localhost](http://localhost) in your browser.

**Expected output:**

```
Hello from CI/CD Pipeline
```

> Nginx is now serving your local `index.html` directly from your host machine via a bind mount.

---

### Step 5 — Update the HTML and see live changes

Edit the file on your host:

```bash
nano html/index.html
```

Refresh the browser — changes appear instantly with **no container restart needed**.

---

### Step 6 — Remove the Nginx container

```bash
docker rm -f nginx-container
```

**Output:**

```
nginx-container
```

---

## Part 2 — tmpfs Mount (Temporary In-Memory Storage)

A **tmpfs mount** stores data in the container's memory only. It is fast, never written to disk, and is **completely wiped** when the container stops or is removed.

### Step 1 — Run a container with a tmpfs mount

```bash
docker run -d \
  --name temp-container \
  --tmpfs /app/cache \
  nginx
```

**Flag breakdown:**

| Flag | Description |
|------|-------------|
| `-d` | Run in detached mode |
| `--name temp-container` | Name the container `temp-container` |
| `--tmpfs /app/cache` | Mount an in-memory tmpfs filesystem at `/app/cache` |

---

### Step 2 — Exec into the container and write a file

```bash
docker exec -it temp-container /bin/bash
```

Inside the container:

```bash
cd /app/cache
echo "Temporary data" > temp.txt
ls
```

**Output:**

```
temp.txt
```

Exit the container:

```bash
exit
```

---

### Step 3 — Remove the container

```bash
docker rm -f temp-container
```

**Output:**

```
temp-container
```

---

### Step 4 — Verify the data is gone

Run a new container with the same tmpfs path:

```bash
docker run -it --tmpfs /app/cache nginx /bin/bash
```

Inside the container:

```bash
cd /app/cache
ls
```

**Output:**

```
(empty)
```

> `temp.txt` is gone — tmpfs data does not persist. It only exists while the container is alive.

---

## 💡 Mount Types Comparison

| Feature | Bind Mount | Named Volume | tmpfs Mount |
|---------|-----------|--------------|-------------|
| Storage location | Host filesystem | Docker-managed disk | Container memory |
| Data persists after container removed | Yes | Yes | No |
| Accessible from host | Yes | Indirect | No |
| Performance | Normal | Normal | Fast |
| Best for | Local dev, static files | App data, databases | Temp cache, secrets |

---

## Key Commands Reference

```bash
# Run Nginx with a bind mount
docker run -d --name nginx-container \
  -v "$(pwd)/html:/usr/share/nginx/html" nginx

# Run a container with tmpfs mount
docker run -d --name temp-container --tmpfs /app/cache nginx

# Exec into a running container
docker exec -it <container-name> /bin/bash

# Force remove a container
docker rm -f <container-name>

# List running containers
docker ps

# List all containers including stopped
docker ps -a
```

---



<img width="1470" height="67" alt="Screenshot 2026-04-23 at 1 12 58 PM" src="https://github.com/user-attachments/assets/6333aa0b-3ba2-474d-8038-b154107bb09f" />


<img width="1470" height="29" alt="Screenshot 2026-04-23 at 1 12 39 PM" src="https://github.com/user-attachments/assets/53322a95-3aaf-4e38-88b9-340a21845ff5" />


<img width="1470" height="98" alt="Screenshot 2026-04-23 at 1 12 24 PM" src="https://github.com/user-attachments/assets/d2123393-5dcb-43c3-940d-17dd4e4b384c" />


<img width="1470" height="112" alt="Screenshot 2026-04-23 at 1 12 11 PM" src="https://github.com/user-attachments/assets/c6946fcf-f806-4ac5-95dd-54501a0001d9" />


<img width="1468" height="874" alt="Screenshot 2026-04-23 at 1 10 01 PM" src="https://github.com/user-attachments/assets/ff917707-a703-499a-94db-a804ae260995" />


<img width="1467" height="355" alt="Screenshot 2026-04-23 at 1 09 49 PM" src="https://github.com/user-attachments/assets/0855970c-b2a0-4c09-b648-8be2805d7147" />


<img width="1469" height="96" alt="Screenshot 2026-04-23 at 1 09 33 PM" src="https://github.com/user-attachments/assets/c3b10591-0c10-41c8-815e-e69798286c44" />


<img width="1470" height="54" alt="Screenshot 2026-04-23 at 1 09 21 PM" src="https://github.com/user-attachments/assets/50fc519c-bc29-4b0f-9b6b-71ab2b634703" />












