# EXPERIMENT - 11 WordPress with Docker Compose & Docker Swarm

A step-by-step guide to deploying WordPress with MySQL using Docker Compose, then scaling it with Docker Swarm.

---

## Prerequisites

- Docker Desktop installed (tested with **Docker v29.2.1**, **Docker Compose v5.0.2**)
- Basic familiarity with the terminal

---

## Project Structure

```
wordpress-docker/
└── docker-compose.yml
```

---

## Step 1 — Create the Project Directory

```bash
mkdir wordpress-docker
cd wordpress-docker
```

---

## Step 2 — Create the Docker Compose File

```bash
touch docker-compose.yml
open -e docker-compose.yml   # Opens in TextEdit on macOS
```

Paste the following configuration:

```yaml
version: '3.9'

services:
  db:
    image: mysql:5.7
    container_name: wordpress_db
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: rootpass
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wpuser
      MYSQL_PASSWORD: wppass
    volumes:
      - db_data:/var/lib/mysql

  wordpress:
    image: wordpress:latest
    container_name: wordpress_app
    restart: always
    depends_on:
      - db
    ports:
      - "8080:80"
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: wpuser
      WORDPRESS_DB_PASSWORD: wppass
      WORDPRESS_DB_NAME: wordpress
    volumes:
      - wp_data:/var/www/html

volumes:
  db_data:
  wp_data:
```

> **Note:** Port `8080` is used because port `80` may already be occupied by another service. Adjust as needed.

---

## Step 3 — Start the Containers

```bash
docker compose up -d
```

Expected output:

```
[+] up 4/4
 ✔ Network wordpress-docker_default   Created
 ✔ Volume wordpress-docker_db_data    Created
 ✔ Container wordpress-db             Created
 ✔ Container wordpress-app            Created
```

### Port Conflict Error?

If you see:
```
Bind for 0.0.0.0:8080 failed: port is already in use
```

Check what's already running:
```bash
docker ps
```
Then change the host port in `docker-compose.yml` (e.g., `"8181:80"`) or stop the conflicting container.

---

## Step 4 — Verify WordPress is Running

Open your browser and navigate to:

```
http://localhost:8080
```

You should see the WordPress setup wizard. 

---

## Step 5 — Tear Down (Docker Compose)

To stop and remove all containers, volumes, and networks created by Compose:

```bash
docker compose down -v
```

Expected output:

```
[+] down 4/4
 ✔ Container wordpress-app            Removed
 ✔ Container wordpress-db             Removed
 ✔ Volume wordpress-docker_db_data    Removed
 ✔ Network wordpress-docker_default   Removed
```

---

## Step 6 — Deploy with Docker Swarm

Docker Swarm allows you to run and scale services across multiple nodes.

### Initialize Swarm

```bash
docker swarm init
```

> If you see `This node is already part of a swarm`, you're good to go. You can verify with:
> ```bash
> docker node ls
> ```

### Deploy the Stack

```bash
docker stack deploy -c docker-compose.yml mystack
```

> **Note:** Swarm ignores some Compose-only options like `restart` and `container_name`. These warnings are safe to ignore.

### Check Services

```bash
docker stack services mystack
```

Example output:

```
ID             NAME                MODE         REPLICAS   IMAGE              PORTS
v4ty3jhiyhj1   mystack_db          replicated   1/1        mysql:8.0
k1ml9xmh4w21   mystack_mysql       replicated   0/1        mysql:5.7
i9kii0v4oeem   mystack_wordpress   replicated   1/1        wordpress:latest   *:8000->80/tcp
```

### Check Individual Task Status

```bash
docker stack ps mystack
```

---

## Step 7 — Scale WordPress Replicas

To scale the WordPress service to 3 replicas:

```bash
docker service scale mystack_wordpress=3
```

Expected output:

```
mystack_wordpress scaled to 3
overall progress: 3 out of 3 tasks
1/3: running
2/3: running
3/3: running
verify: Service mystack_wordpress converged
```

### Verify Running Containers

```bash
docker ps | grep wordpress
```

You should see 3 `wordpress:latest` containers running.

---

## Step 8 — View All Services

```bash
docker service ls
```

Example output:

```
ID             NAME                MODE         REPLICAS   IMAGE              PORTS
v4ty3jhiyhj1   mystack_db          replicated   1/1        mysql:8.0
k1ml9xmh4w21   mystack_mysql       replicated   0/1        mysql:5.7
i9kii0v4oeem   mystack_wordpress   replicated   3/3        wordpress:latest   *:8000->80/tcp
woz8dlgkh1io   myweb               replicated   1/1        nginx:latest       *:8080->80/tcp
```

---

## Step 9 — Remove the Stack

```bash
docker stack rm mystack
```

This removes all services and the default network for the stack:

```
Removing service mystack_db
Removing service mystack_mysql
Removing service mystack_wordpress
Removing network mystack_default
```

---

## Quick Reference

| Task | Command |
|------|---------|
| Start (Compose) | `docker compose up -d` |
| Stop & clean (Compose) | `docker compose down -v` |
| View containers | `docker ps` |
| Init Swarm | `docker swarm init` |
| Deploy stack | `docker stack deploy -c docker-compose.yml mystack` |
| List stack services | `docker stack services mystack` |
| Scale a service | `docker service scale mystack_wordpress=N` |
| Remove stack | `docker stack rm mystack` |
| Leave swarm | `docker swarm leave --force` |

---

## Troubleshooting

**Port already allocated**
Check `docker ps` for containers using the conflicting port and stop them, or change the port mapping in `docker-compose.yml`.

**`mysql:5.7` replica stuck in Pending / "no suitable node (unsupported)"**
This can happen on Apple Silicon (ARM) Macs where `mysql:5.7` has no native ARM image. Use `mysql:8.0` or add `platform: linux/amd64` to the service definition.

**WordPress container shows `80/tcp` but no external port**
In Swarm mode, use `ports` with the `*:HOST->CONTAINER/tcp` format. Compose-style short syntax may not bind correctly in stack deployments.




<img width="1468" height="73" alt="Screenshot 2026-04-25 at 9 26 25 AM" src="https://github.com/user-attachments/assets/7de2cba6-8212-4424-bddf-bc55c3309a16" />
<img width="1469" height="273" alt="Screenshot 2026-04-25 at 9 22 52 AM" src="https://github.com/user-attachments/assets/6f107bc4-9809-4cd7-9862-9c2cd9889ccd" />
<img width="1468" height="94" alt="Screenshot 2026-04-25 at 9 14 49 AM" src="https://github.com/user-attachments/assets/b33b7cb6-f36d-4148-b269-929fdf31bb3c" />
<img width="1467" height="99" alt="Screenshot 2026-04-25 at 9 14 21 AM" src="https://github.com/user-attachments/assets/57e4ff70-3096-483d-8bb3-d2e6f55f9a67" />
<img width="1464" height="157" alt="Screenshot 2026-04-25 at 9 14 09 AM" src="https://github.com/user-attachments/assets/fe15b59a-0acc-4ac6-a32b-945b50eb6643" />
<img width="1470" height="150" alt="Screenshot 2026-04-25 at 9 13 51 AM" src="https://github.com/user-attachments/assets/d413667f-35ae-4e79-bc35-a212dc04dc4b" />
<img width="1467" height="74" alt="Screenshot 2026-04-25 at 9 13 35 AM" src="https://github.com/user-attachments/assets/aa2f497b-81a6-45dc-95b5-e272ad5f6e2b" />
<img width="1470" height="574" alt="Screenshot 2026-04-25 at 9 13 19 AM" src="https://github.com/user-attachments/assets/997a7992-7c34-49e3-8da1-182ecf3a0b81" />
<img width="1463" height="872" alt="Screenshot 2026-04-25 at 9 08 35 AM" src="https://github.com/user-attachments/assets/51f0ba72-db28-4cb2-b933-de39f021fd3f" />
<img width="1470" height="626" alt="Screenshot 2026-04-25 at 9 08 17 AM" src="https://github.com/user-attachments/assets/bebed8c1-fc84-4934-aaa8-087faecce453" />
<img width="1470" height="956" alt="Screenshot 2026-04-25 at 9 04 39 AM" src="https://github.com/user-attachments/assets/cb8f4142-9adf-450d-87f5-a65349f8d731" />
<img width="1468" height="72" alt="Screenshot 2026-04-25 at 9 04 31 AM" src="https://github.com/user-attachments/assets/1fe27ab2-2691-4986-8b05-3785f6ed45b2" />
<img width="1303" height="83" alt="Screenshot 2026-04-25 at 9 04 14 AM" src="https://github.com/user-attachments/assets/6c93e2b1-b553-4bfc-b2eb-3eecffd971b1" />



