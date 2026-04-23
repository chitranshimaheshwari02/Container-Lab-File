# Docker Volumes

> Learn how Docker volumes work — create named volumes, mount them into containers, persist data across container lifecycles, and use bind mounts to share local folders with containers.

---

## What You Will Learn

- Create and list Docker named volumes
- Mount a volume into a container and write data
- Verify data persists after a container is removed
- Use a bind mount to share a local folder with a container
- Read data written inside a container from the host machine

---

## Prerequisites

- [Docker Desktop](https://www.docker.com/products/docker-desktop/) installed and running
- Basic familiarity with the terminal

---

## Part 1 — Named Volumes

### Step 1 — Create a named volume

```bash
docker volume create firstvol
```

**Output:**

```
firstvol
```

---

### Step 2 — List all volumes

```bash
docker volume ls
```

**Output:**

```
DRIVER    VOLUME NAME
local     firstvol
local     dbdata
local     mydata
...
```

> Your `firstvol` volume is now created and managed by Docker on your local machine.

---

### Step 3 — Run a container and mount the volume

Start an interactive Ubuntu container with `firstvol` mounted at `/home/app`:

```bash
docker run -it --name volcontainer -v firstvol:/home/app ubuntu /bin/bash
```

Inside the container, navigate to the mounted path and write a file:

```bash
cd /home/app
echo "Hello from Volume Container 1" > file1.txt
ls
```

**Output:**

```
file1.txt
```

Exit the container:

```bash
exit
```

---

### Step 4 — Check all containers

```bash
docker ps -a
```

You should see `volcontainer` with status `Exited (0)`:

```
CONTAINER ID   IMAGE    COMMAND       STATUS                    NAMES
2ceb86c1e4ab   ubuntu   "/bin/bash"   Exited (0) 4 seconds ago  volcontainer
```

---

### Step 5 — Remove the first container

```bash
docker rm volcontainer
```

**Output:**

```
volcontainer
```

---

### Step 6 — Run a new container with the same volume

The data written by `volcontainer` should still be there:

```bash
docker run -it --name volcontainer2 -v firstvol:/home/app ubuntu /bin/bash
```

Inside the new container:

```bash
cd /home/app
ls
```

**Output:**

```
file1.txt
```

> `file1.txt` persists even though the original container was deleted — this is the power of Docker volumes.

---

## Part 2 — Bind Mounts (Local Folder)

A **bind mount** maps a folder on your host machine directly into the container, so files written inside the container are immediately accessible on your host.

### Step 1 — Create a local folder

```bash
mkdir firstvol
```

---

### Step 2 — Run a container with the local folder as a bind mount

```bash
docker run -it --rm -v "$(pwd)/firstvol:/home/app" ubuntu /bin/bash
```

Inside the container:

```bash
cd /home/app
echo "Hello from Local Volume" > local.txt
ls
```

**Output:**

```
local.txt
```

Exit the container:

```bash
exit
```

---

### Step 3 — Verify the file exists on the host

```bash
ls firstvol
```

**Output:**

```
local.txt
```

Read its contents:

```bash
cat firstvol/local.txt
```

**Output:**

```
Hello from Local Volume
```

> The file written inside the container is immediately accessible on the host — bind mounts are two-way.

---

## Named Volume vs Bind Mount

| Feature | Named Volume | Bind Mount |
|---------|-------------|------------|
| Managed by Docker | Yes  No |
| Data persists after container removed | Yes | Yes |
| Shared between containers | Yes | Yes |
| Access from host filesystem | Indirect |  Direct |
| Best for | Databases, app data | Local dev, config files |

---

## Key Commands Reference

```bash
# Create a named volume
docker volume create <volume-name>

# List all volumes
docker volume ls

# Mount a named volume into a container
docker run -it -v <volume-name>:/path/in/container <image> /bin/bash

# Mount a local folder as a bind mount
docker run -it -v "$(pwd)/folder:/path/in/container" <image> /bin/bash

# Remove a container
docker rm <container-name>

# List all containers (including stopped)
docker ps -a
```

---


<img width="1466" height="59" alt="Screenshot 2026-04-23 at 12 58 43 PM" src="https://github.com/user-attachments/assets/1d1819a5-f307-4031-9e46-df1c31a21406" />


<img width="1466" height="83" alt="Screenshot 2026-04-23 at 12 58 34 PM" src="https://github.com/user-attachments/assets/c3c3f1c0-4832-4377-b893-895e6c6cd6f0" />


<img width="1469" height="87" alt="Screenshot 2026-04-23 at 12 58 20 PM" src="https://github.com/user-attachments/assets/f94c89ae-e10e-4ddc-a378-15335b575ab7" />


<img width="1464" height="602" alt="Screenshot 2026-04-23 at 12 58 03 PM" src="https://github.com/user-attachments/assets/607e968f-b963-43dc-a3d6-732b3b3e021a" />


<img width="1469" height="97" alt="Screenshot 2026-04-23 at 12 57 39 PM" src="https://github.com/user-attachments/assets/a3c2cbd6-5d91-4552-bef2-95f251b38a6e" />


<img width="1468" height="376" alt="Screenshot 2026-04-23 at 12 57 22 PM" src="https://github.com/user-attachments/assets/ff86266d-80c0-4b9a-b78d-ee193fed658a" />


<img width="1470" height="53" alt="Screenshot 2026-04-23 at 12 57 12 PM" src="https://github.com/user-attachments/assets/e53fdd17-4cf8-4ca3-9b12-d6b1681de60c" />












