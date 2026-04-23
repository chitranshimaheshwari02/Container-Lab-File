Docker Python Runtime
A lightweight Docker environment for running Python applications with NumPy support — ideal for scientific computing, data processing, and reproducible runtime environments.

Project Structure
docker-python-runtime/
├── app.py        
└── Dockerfile    

Prerequisites

Docker Desktop installed and running
Basic familiarity with the terminal


Getting Started
1. Clone or Set Up the Project
bashmkdir docker-python-runtime
cd docker-python-runtime
touch app.py
touch Dockerfile
2. Write Your Python App
Edit app.py with your Python code. Example used in this project:
pythonimport numpy as np

print("Python app is running inside Docker!")

arr = np.array([1, 2, 3, 4])
print("Numpy Array:", arr)
print("Sum:", np.sum(arr))
3. Define the Dockerfile
dockerfileFROM docker.io/library/python:3.8-slim

WORKDIR /home/app

RUN pip install numpy

Build the Docker Image
bashdocker build -t sapid-checker-runtime:500119484 .
What happens during the build:

Pulls python:3.8-slim base image (~14.5 MB)
Sets /home/app as the working directory
Installs numpy via pip


Build time: ~113 seconds on first run (cached on subsequent builds)


Run the Container
bashsudo docker run -it -v "$(pwd)/app.py:/home/app/app.py" sapid-checker-runtime:500119484
Flag Breakdown
FlagDescription-itInteractive terminal mode-v "$(pwd)/app.py:/home/app/app.py"Mounts your local app.py into the container
Expected Output
Python app is running inside Docker!
Numpy Array: [1 2 3 4]
Sum: 10

How It Works

A minimal Python 3.8 image is used as the base to keep the image size small.
NumPy is installed at build time inside the image.
At runtime, the local app.py is volume-mounted into the container — meaning you can edit the file locally and re-run without rebuilding the image.




<img width="1466" height="66" alt="Screenshot 2026-04-23 at 12 32 39 PM" src="https://github.com/user-attachments/assets/ed970f37-2bb3-4fc9-a400-ef93c5a2598f" />
<img width="1462" height="55" alt="Screenshot 2026-04-23 at 12 32 17 PM" src="https://github.com/user-attachments/assets/974a07aa-d258-4908-8536-407edbb968fa" />
<img width="1469" height="392" alt="Screenshot 2026-04-23 at 12 31 57 PM" src="https://github.com/user-attachments/assets/f2e05880-8e5d-48da-ac0d-21f270354a9b" />
<img width="1470" height="112" alt="Screenshot 2026-04-23 at 12 31 41 PM" src="https://github.com/user-attachments/assets/48cd4f23-3820-47d4-8923-bab6e11c4831" />




📦 Dependencies
PackageVersionPython3.8-slimNumPyLatest (installed via pip)
