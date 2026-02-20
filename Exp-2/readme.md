### Step 1: Verify Docker Installation

To ensure Docker is installed correctly, the following command was executed:

```bash
docker --version
```

#### Output:
This command displays the installed Docker version, confirming successful installation.

---

### Step 2: Check Docker System Information

To verify Docker configuration and system details:

```bash
docker info
```

#### Output:
Displays:
- Number of containers
- Number of images
- Storage driver
- Docker root directory
- Server version

This confirms Docker is running properly.

---

### Step 3: Pull Docker Image from Docker Hub

To download an image from Docker Hub:

```bash
docker pull nginx
```

#### Explanation:
- `docker pull` downloads an image.
- `nginx` is the official NGINX web server image.

#### Verification:
Check downloaded images:

```bash
docker images
```

This lists all available Docker images.

---

### Step 4: Run Docker Container with Port Mapping

To run the NGINX container:

```bash
docker run -d -p 8080:80 --name mynginx nginx
```

#### Explanation:
- `-d` → Run container in detached mode
- `-p 8080:80` → Maps port 8080 (host) to port 80 (container)
- `--name mynginx` → Assigns container name
- `nginx` → Image used

---

### Step 5: Verify Container is Running

Check running containers:

```bash
docker ps
```

#### Output:
Displays running container details:
- Container ID
- Image
- Status
- Ports
- Names

---

### Step 6: Verify in Browser

Open browser and visit:

```
http://localhost:8080
```

If successful, the NGINX welcome page appears, confirming the container is running correctly.

---

### Step 7: Stop Running Container

To stop the container:

```bash
docker stop mynginx
```

---

### Step 8: Remove Container

After stopping, remove the container:

```bash
docker rm mynginx
```

---

### Step 9: Remove Docker Image

To delete the downloaded image:

```bash
docker rmi nginx
```

If error occurs (image in use), ensure container is removed first.


## Conclusion

In this experiment, Docker was successfully installed and configured.  
We performed basic container lifecycle operations including:

- Pulling images  
- Running containers  
- Port mapping  
- Verifying through browser  
- Stopping and removing containers  
- Removing images  

This experiment helped in understanding the foundational concepts of containerization and Docker usage in DevOps workflows.

## Screenshots

<img width="1470" height="956" alt="Screenshot 2026-01-31 at 8 59 38 AM" src="https://github.com/user-attachments/assets/f62bb17c-a52d-4cb8-9c02-9f210f4d376d" />

<img width="1470" height="956" alt="1" src="https://github.com/user-attachments/assets/4d338995-1e19-4796-8544-2790267865d9" />

<img width="1470" height="956" alt="2" src="https://github.com/user-attachments/assets/2fef24a6-2429-467a-9bd9-3e1f019cb7fc" />

<img width="1470" height="956" alt="Screenshot 2026-01-31 at 8 52 40 AM" src="https://github.com/user-attachments/assets/fd7063a8-6d7f-4cc3-ae37-69f42e34545b" />

