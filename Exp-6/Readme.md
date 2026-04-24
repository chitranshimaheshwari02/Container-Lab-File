# Experiment 6: Docker Run vs Docker Compose

> A hands-on comparison of imperative (`docker run`) and declarative (`docker-compose.yml`) approaches to container management — from single containers to multi-stage production builds.

---

## Table of Contents

- [Theory](#-theory)
- [Mapping: docker run → Docker Compose](#-mapping-docker-run--docker-compose)
- [Part B – Practical Tasks](#-part-b--practical-tasks)
  - [Task 1: Single Container Comparison](#task-1-single-container-comparison)
  - [Task 2: Multi-Container Application](#task-2-multi-container-application)
- [Part C – Conversion Tasks](#-part-c--conversion--build-based-tasks)
  - [Task 3: Convert docker run to Compose](#task-3-convert-docker-run-to-docker-compose)
  - [Task 4: Resource Limits Conversion](#task-4-resource-limits-conversion)
- [Part D – Dockerfile-Based Builds](#-part-d--using-dockerfile-instead-of-standard-image)
  - [Task 5: Custom Node.js App](#task-5-replace-standard-image-with-dockerfile-node-app)
  - [Task 6: Multi-Stage Build with Compose](#task-6-multi-stage-dockerfile-with-compose)
- [Compose Mode vs Swarm Mode](#-compose-mode-vs-swarm-mode)

---

## Theory

### The Two Approaches

| Approach | Style | Command |
|---|---|---|
| `docker run` | **Imperative** — specify step-by-step instructions | `docker run [flags] image` |
| Docker Compose | **Declarative** — define the desired state in YAML | `docker compose up -d` |

### docker run (Imperative)

Every configuration detail is passed as a CLI flag:

```bash
docker run -d \
  --name my-nginx \
  -p 8080:80 \
  -v ./html:/usr/share/nginx/html \
  -e NGINX_HOST=localhost \
  --restart unless-stopped \
  nginx:alpine
```

### Docker Compose (Declarative)

The same configuration lives in a readable, version-controlled YAML file:

```yaml
version: '3.8'

services:
  nginx:
    image: nginx:alpine
    container_name: my-nginx
    ports:
      - "8080:80"
    volumes:
      - ./html:/usr/share/nginx/html
    environment:
      NGINX_HOST: localhost
    restart: unless-stopped
```

### Advantages of Docker Compose

- Simplifies multi-container applications into a single file
- Fully reproducible and version-controllable
- Unified lifecycle management (`up`, `down`, `logs`, `ps`)
- Supports service scaling with one command:

```bash
docker compose up --scale web=3
```

---

## Mapping: docker run → Docker Compose

| `docker run` Flag | Docker Compose Equivalent |
|---|---|
| `-p 8080:80` | `ports:` |
| `-v host:container` | `volumes:` |
| `-e KEY=value` | `environment:` |
| `--name` | `container_name:` |
| `--network` | `networks:` |
| `--restart` | `restart:` |
| `--memory` | `deploy.resources.limits.memory` |
| `--cpus` | `deploy.resources.limits.cpus` |
| `-d` | `docker compose up -d` |

---

## Part B – Practical Tasks

### Task 1: Single Container Comparison

Deploy an Nginx server both ways and compare.

#### A. Using `docker run`

```bash
docker run -d \
  --name lab-nginx \
  -p 8081:80 \
  -v $(pwd)/html:/usr/share/nginx/html \
  nginx:alpine
```

```bash
docker ps                  # verify
# open http://localhost:8081

docker stop lab-nginx
docker rm lab-nginx
```

#### B. Using Docker Compose

Create `docker-compose.yml`:

```yaml
version: '3.8'

services:
  nginx:
    image: nginx:alpine
    container_name: lab-nginx
    ports:
      - "8081:80"
    volumes:
      - ./html:/usr/share/nginx/html
```

```bash
docker compose up -d       # start
docker compose ps          # verify
docker compose down        # stop and remove
```

---

### Task 2: Multi-Container Application

Deploy **WordPress + MySQL** both ways.

#### A. Using `docker run`

```bash
# Create shared network
docker network create wp-net

# Start MySQL
docker run -d \
  --name mysql \
  --network wp-net \
  -e MYSQL_ROOT_PASSWORD=secret \
  -e MYSQL_DATABASE=wordpress \
  mysql:5.7

# Start WordPress
docker run -d \
  --name wordpress \
  --network wp-net \
  -p 8082:80 \
  -e WORDPRESS_DB_HOST=mysql \
  -e WORDPRESS_DB_PASSWORD=secret \
  wordpress:latest
```

> Open **http://localhost:8082** to access WordPress.

#### B. Using Docker Compose

```yaml
version: '3.8'

services:
  mysql:
    image: mysql:5.7
    environment:
      MYSQL_ROOT_PASSWORD: secret
      MYSQL_DATABASE: wordpress
    volumes:
      - mysql_data:/var/lib/mysql

  wordpress:
    image: wordpress:latest
    ports:
      - "8082:80"
    environment:
      WORDPRESS_DB_HOST: mysql
      WORDPRESS_DB_PASSWORD: secret
    depends_on:
      - mysql

volumes:
  mysql_data:
```

```bash
docker compose up -d       # start everything
docker compose down -v     # stop and remove volumes
```

---

## Part C – Conversion & Build-Based Tasks

### Task 3: Convert docker run to Docker Compose

#### Problem 1: Basic Web Application

**Given:**

```bash
docker run -d \
  --name webapp \
  -p 5000:5000 \
  -e APP_ENV=production \
  -e DEBUG=false \
  --restart unless-stopped \
  node:18-alpine
```

**Solution — `docker-compose.yml`:**

```yaml
version: '3.8'

services:
  webapp:
    image: node:18-alpine
    container_name: webapp
    ports:
      - "5000:5000"
    environment:
      APP_ENV: production
      DEBUG: "false"
    restart: unless-stopped
```

```bash
docker compose up -d
docker compose ps
```

---

#### Problem 2: Volume + Network Configuration

**Given:**

```bash
docker network create app-net

docker run -d \
  --name postgres-db \
  --network app-net \
  -e POSTGRES_USER=admin \
  -e POSTGRES_PASSWORD=secret \
  -v pgdata:/var/lib/postgresql/data \
  postgres:15

docker run -d \
  --name backend \
  --network app-net \
  -p 8000:8000 \
  -e DB_HOST=postgres-db \
  -e DB_USER=admin \
  -e DB_PASS=secret \
  python:3.11-slim
```

**Solution — `docker-compose.yml`:**

```yaml
version: '3.8'

services:
  postgres-db:
    image: postgres:15
    container_name: postgres-db
    environment:
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: secret
    volumes:
      - pgdata:/var/lib/postgresql/data
    networks:
      - app-net

  backend:
    image: python:3.11-slim
    container_name: backend
    ports:
      - "8000:8000"
    environment:
      DB_HOST: postgres-db
      DB_USER: admin
      DB_PASS: secret
    depends_on:
      - postgres-db
    networks:
      - app-net

volumes:
  pgdata:

networks:
  app-net:
```

---

### Task 4: Resource Limits Conversion

**Given:**

```bash
docker run -d \
  --name limited-app \
  -p 9000:9000 \
  --memory="256m" \
  --cpus="0.5" \
  --restart always \
  nginx:alpine
```

**Solution — `docker-compose.yml`:**

```yaml
version: '3.8'

services:
  limited-app:
    image: nginx:alpine
    container_name: limited-app
    ports:
      - "9000:9000"
    restart: always
    deploy:
      resources:
        limits:
          memory: 256M
          cpus: "0.5"
```

> **Note:** The `deploy.resources` section only takes effect in **Docker Swarm mode** (`docker stack deploy`). It is ignored in standard `docker compose up`.

---

## Part D – Using Dockerfile Instead of Standard Image

### Task 5: Replace Standard Image with Dockerfile (Node App)

Instead of using `image: node:18-alpine` directly, build your own image.

**`app.js`:**

```javascript
const http = require('http');

http.createServer((req, res) => {
  res.end("Docker Compose Build Lab");
}).listen(3000);
```

**`Dockerfile`:**

```dockerfile
FROM node:18-alpine

WORKDIR /app

COPY app.js .

EXPOSE 3000

CMD ["node", "app.js"]
```

**`docker-compose.yml`:**

```yaml
version: '3.8'

services:
  nodeapp:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: custom-node-app
    ports:
      - "3000:3000"
```

```bash
docker compose up --build -d
# open http://localhost:3000
```

To see live changes: edit `app.js`, then re-run `docker compose up --build -d`.

---

### Task 6: Multi-Stage Dockerfile with Compose

Use a multi-stage build to produce a lean production image for a Python FastAPI app.

**`Dockerfile`:**

```dockerfile
# ---------- Stage 1: Builder ----------
FROM python:3.11-slim AS builder

WORKDIR /app

COPY app/requirements.txt .

RUN pip install --upgrade pip \
    && pip install --prefix=/install -r requirements.txt

# ---------- Stage 2: Production ----------
FROM python:3.11-slim

WORKDIR /app

COPY --from=builder /install /usr/local
COPY app/ .

ENV PYTHONUNBUFFERED=1

EXPOSE 8000

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

**`docker-compose.yml`:**

```yaml
version: "3.8"

services:
  fastapi-app:
    build: .
    container_name: fastapi-app
    ports:
      - "8000:8000"
    environment:
      APP_ENV: production
    volumes:
      - ./app:/app   # Mount for development hot-reload
    restart: unless-stopped
```

```bash
docker compose up --build -d

# Compare image sizes
docker images
```

---

## Compose Mode vs Swarm Mode

| Feature | Normal Compose Mode | Swarm Mode |
|---|---|---|
| **Use case** | Local development | Production / clustering |
| **Nodes** | Single node only | Multi-node |
| **`deploy:` section** | Ignored | Applied |
| **Scaling** | `--scale` flag | Rolling updates via Swarm |
| **Command** | `docker compose up` | `docker stack deploy` |





<img width="1470" height="82" alt="Screenshot 2026-04-24 at 11 09 58 PM" src="https://github.com/user-attachments/assets/5fdcf09e-5f56-4b1d-bfef-e78372222e15" />
<img width="1470" height="115" alt="Screenshot 2026-04-24 at 11 09 44 PM" src="https://github.com/user-attachments/assets/7af1716d-5f3c-4bd4-8449-6120aa2e98eb" />
<img width="1470" height="875" alt="Screenshot 2026-04-24 at 11 35 01 PM" src="https://github.com/user-attachments/assets/2a0a495c-b22e-4905-9620-4150880fa35d" />
<img width="1469" height="454" alt="Screenshot 2026-04-24 at 11 34 43 PM" src="https://github.com/user-attachments/assets/ed32fb53-6cf8-4c87-981d-45dd08eab761" />
<img width="1470" height="956" alt="Screenshot 2026-04-24 at 11 32 36 PM" src="https://github.com/user-attachments/assets/4fc3d544-6e20-41bf-96b9-3a540d28128a" />
<img width="1464" height="27" alt="Screenshot 2026-04-24 at 11 32 29 PM" src="https://github.com/user-attachments/assets/2209f171-abf3-4b42-a703-492eb99faca3" />
<img width="1470" height="956" alt="Screenshot 2026-04-24 at 11 31 47 PM" src="https://github.com/user-attachments/assets/9c470c30-72d8-4c2f-aaaf-16bd44c73444" />
<img width="1456" height="38" alt="Screenshot 2026-04-24 at 11 31 34 PM" src="https://github.com/user-attachments/assets/53b0aa29-39c2-4408-8651-dd5d5f5c87bf" />
<img width="1470" height="956" alt="Screenshot 2026-04-24 at 11 30 48 PM" src="https://github.com/user-attachments/assets/4b293750-9abe-498d-a653-36c7dfe1d029" />
<img width="1470" height="78" alt="Screenshot 2026-04-24 at 11 30 41 PM" src="https://github.com/user-attachments/assets/e3a9cbae-c494-429a-92f7-8630b1de8b67" />
<img width="1470" height="956" alt="Screenshot 2026-04-24 at 11 28 33 PM" src="https://github.com/user-attachments/assets/7222ada6-c838-4db0-8801-33b407b4fe4e" />
<img width="1464" height="420" alt="Screenshot 2026-04-24 at 11 28 26 PM" src="https://github.com/user-attachments/assets/5be91330-6521-4ca1-9be0-0c2018169b47" />
<img width="1470" height="956" alt="Screenshot 2026-04-24 at 11 27 15 PM" src="https://github.com/user-attachments/assets/de1dc7ca-f72e-4e93-97fb-7b52df1e25f4" />
<img width="1460" height="30" alt="Screenshot 2026-04-24 at 11 27 11 PM" src="https://github.com/user-attachments/assets/81ac3756-1fe9-4ff2-97f9-99b5c399f6e0" />
<img width="1470" height="956" alt="Screenshot 2026-04-24 at 11 26 36 PM" src="https://github.com/user-attachments/assets/e79f5b65-c1e6-42ed-b43c-bca570070c7c" />
<img width="1463" height="170" alt="Screenshot 2026-04-24 at 11 26 30 PM" src="https://github.com/user-attachments/assets/37074a3e-9846-47b3-81b6-bd37e1ccd17e" />
<img width="1466" height="44" alt="Screenshot 2026-04-24 at 11 24 27 PM" src="https://github.com/user-attachments/assets/b71d1848-f5e8-4d5f-a29c-964a3493d37e" />
<img width="1469" height="74" alt="Screenshot 2026-04-24 at 11 23 57 PM" src="https://github.com/user-attachments/assets/eea21027-a81b-4c08-b937-759f2e891587" />
<img width="1470" height="956" alt="Screenshot 2026-04-24 at 11 23 50 PM" src="https://github.com/user-attachments/assets/c1c90f86-3624-4bb1-a581-b0240da59f68" />
<img width="1464" height="96" alt="Screenshot 2026-04-24 at 11 23 45 PM" src="https://github.com/user-attachments/assets/02096b59-a081-40dc-a43d-26240aa02b01" />
<img width="1470" height="140" alt="Screenshot 2026-04-24 at 11 23 33 PM" src="https://github.com/user-attachments/assets/aa5f89d8-da43-49a1-9dff-c43b7f3aadf3" />
<img width="1470" height="878" alt="Screenshot 2026-04-24 at 11 22 31 PM" src="https://github.com/user-attachments/assets/c2255427-350c-497c-9e56-d0428c9487db" />
<img width="1470" height="324" alt="Screenshot 2026-04-24 at 11 22 12 PM" src="https://github.com/user-attachments/assets/fa679aba-dac3-4d99-958f-3e857eec7d59" />
<img width="1470" height="34" alt="Screenshot 2026-04-24 at 11 21 04 PM" src="https://github.com/user-attachments/assets/1aa63114-5e7d-4e4c-9fc4-6797d57ee286" />
<img width="1470" height="956" alt="Screenshot 2026-04-24 at 11 11 48 PM" src="https://github.com/user-attachments/assets/ee71f66e-23c3-4706-ace7-7b2d8b75ca28" />
<img width="1470" height="114" alt="Screenshot 2026-04-24 at 11 11 31 PM" src="https://github.com/user-attachments/assets/bc381033-25f7-49d7-9734-ba72a4f1ea99" />
<img width="1468" height="98" alt="Screenshot 2026-04-24 at 11 11 11 PM" src="https://github.com/user-attachments/assets/32e2ed6d-01f5-4361-8576-62659b20275c" />
<img width="1467" height="873" alt="Screenshot 2026-04-24 at 11 10 52 PM" src="https://github.com/user-attachments/assets/97ebb204-136b-44fd-9556-86752614c39c" />
<img width="1470" height="460" alt="Screenshot 2026-04-24 at 11 10 14 PM" src="https://github.com/user-attachments/assets/f591cb47-ad1e-4684-ad8a-bd0fce444290" />





















