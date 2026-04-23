# Docker Python Runtime

> Run a Python app inside Docker by attaching it at container runtime — no rebuild needed when your code changes.

---

## Project Structure

```
docker-python-runtime/
├── app.py          # Your Python application
└── Dockerfile      # Docker image definition
```

---

## Prerequisites

- [Docker Desktop](https://www.docker.com/products/docker-desktop/) installed and running
- Basic familiarity with the terminal

---

## Getting Started

### Step 1 — Create the project folder

```bash
mkdir docker-python-runtime
cd docker-python-runtime
touch app.py Dockerfile
```

---

### Step 2 — Write the Python app

Edit `app.py` with the following code:

```python
import numpy as np

print("Python app is running inside Docker!")

arr = np.array([1, 2, 3, 4])
print("Numpy Array:", arr)
print("Sum:", np.sum(arr))
```

---

### Step 3 — Write the Dockerfile

Edit `Dockerfile` with the following:

```dockerfile
FROM python:3.8-slim

WORKDIR /home/app

RUN pip install numpy

CMD ["python", "app.py"]
```

---

### Step 4 — Build the image

Run this from inside your project folder:

```bash
docker build -t sapid-checker-runtime:500119484 .
```

**What happens during the build:**

- Pulls `python:3.8-slim` base image (~14.5 MB)
- Sets `/home/app` as the working directory
- Installs `numpy` via pip

> Build time: ~113 seconds on first run. Subsequent builds use cache and are much faster.

---

### Step 5 — Check the image was created

```bash
docker images
```

You should see your image listed:

```
REPOSITORY              TAG          IMAGE ID
sapid-checker-runtime   500119484    e2f10adad92b
```

---

### Step 6 — Run the image and attach `app.py`

```bash
sudo docker run -it -v "$(pwd)/app.py:/home/app/app.py" sapid-checker-runtime:500119484
```

**Flag breakdown:**

| Flag | Description |
|------|-------------|
| `-it` | Interactive terminal — keeps stdin open |
| `-v "$(pwd)/app.py:/home/app/app.py"` | Mounts your local `app.py` into the container |

---

## Expected Output

```
Python app is running inside Docker!
Numpy Array: [1 2 3 4]
Sum: 10
```

---

## How It Works

The image is built **once** and contains Python + NumPy.  
Your `app.py` lives **outside** the image and is mounted in at runtime using `-v`.  
This means you can edit your Python code and re-run without ever rebuilding the image.

---

## Dependencies

| Package | Version  | Installed via    |
|---------|----------|------------------|
| Python  | 3.8-slim | Base image       |
| NumPy   | Latest   | pip (build time) |








<img width="1466" height="66" alt="Screenshot 2026-04-23 at 12 32 39 PM" src="https://github.com/user-attachments/assets/ed970f37-2bb3-4fc9-a400-ef93c5a2598f" />



<img width="1462" height="55" alt="Screenshot 2026-04-23 at 12 32 17 PM" src="https://github.com/user-attachments/assets/974a07aa-d258-4908-8536-407edbb968fa" />


<img width="1469" height="392" alt="Screenshot 2026-04-23 at 12 31 57 PM" src="https://github.com/user-attachments/assets/f2e05880-8e5d-48da-ac0d-21f270354a9b" />


<img width="1470" height="112" alt="Screenshot 2026-04-23 at 12 31 41 PM" src="https://github.com/user-attachments/assets/48cd4f23-3820-47d4-8923-bab6e11c4831" />




📦 Dependencies
PackageVersionPython3.8-slimNumPyLatest (installed via pip)
