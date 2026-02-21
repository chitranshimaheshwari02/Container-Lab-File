# EXPERIMENT- 4 Dockerizing a Flask Application on macOS

## Containerizing a Flask Web Application using Docker on macOS

---

## Objective

The objective of this experiment is to:

- Develop a simple Flask web application  
- Create a virtual environment for dependency isolation  
- Containerize the application using Docker  
- Build a Docker image  
- Run the application inside a Docker container  
- Understand port mapping and container workflow  

---

## System Requirements

### Hardware
- 64-bit Mac system  
- Minimum 4GB RAM  

### Software
- macOS  
- Python 3 (installed via Homebrew)  
- Docker Desktop for Mac  
- Terminal (zsh/bash)  

---

## Project Structure

my-flask-app/
│
├── app.py
├── requirements.txt
├── Dockerfile
└── README.md

---

## Step 1: Create Flask Application

Create `app.py`:

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def home():
    return "Hello, Docker + Flask!"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)

```

##  Step 2: Create Virtual Environment (macOS)

Since macOS uses an externally managed Python environment, we cannot install packages globally.

### Create Virtual Environment

```bash
python3 -m venv venv
source venv/bin/activate
```
##  Step 3: Run Flask Application Locally

Run the application:

```bash
python app.py
```

## Step 4: Create Requirements File

Generate dependency file:

```bash
pip freeze > requirements.txt
```
## Step 5: Create Dockerfile

Create a file named `Dockerfile` in the root directory of your project and add the following content:

```dockerfile
# Use official Python base image
FROM python:3.9-slim

# Set working directory
WORKDIR /app

# Copy project files into container
COPY . .

# Install dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Expose application port
EXPOSE 5000

# Run the Flask application
CMD ["python", "app.py"]
```

##  Step 6: Build Docker Image

Make sure Docker Desktop is running.

Build the Docker image using:

```bash
docker build -t my-flask-app .
```

## Step 7: Run Docker Container

Run the container using:

```bash
docker run -p 5000:5000 my-flask-app
```

# Node.js Application Dockerization

---

## Objective

The objective of this experiment is to:

- Create a simple Node.js web application
- Run it locally
- Create package.json
- Write a Dockerfile
- Build a Docker image
- Run the container with port mapping
- Verify container execution
- Understand common errors and solutions

---

# Step 1: Create Node.js Project

Create a project folder:

```bash
mkdir my-node-app
cd my-node-app
```

# Step 2: Initialize Node.js Project

Initialize the Node.js project:

```bash
npm init -y
```

# Step 3: Install Express

Install Express (web framework for Node.js):

```bash
npm install express
```

# Step 4: Create Application File (index.js)

Create a file named `index.js` inside your project folder:

```bash
touch index.js

const express = require('express');
const app = express();

const PORT = 3000;

app.get('/', (req, res) => {
  res.send('Hello from Node.js Docker App!');
});

app.get('/health', (req, res) => {
  res.send('OK');
});

app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
```

# Step 5: Run Node.js Application Locally
```bash
node index.js
```

# Step 6: Create .dockerignore File
```bash
touch .dockerignore

node_modules
npm-debug.log
```

# Step 7: Create Dockerfile
```bash
touch Dockerfile

# Use official Node image
FROM node:18-alpine

# Set working directory
WORKDIR /app

# Copy package files
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy remaining files
COPY . .

# Expose port
EXPOSE 3000

# Run application
CMD ["node", "app.js"]
```

# Step 8: Build Docker Image
```bash
docker build -t my-node-app .
```

# Step 9: Run Docker Container
```bash
docker run -p 3000:3000 my-node-app
```

# Screenshots
<img width="1469" height="63" alt="Screenshot 2026-02-21 at 8 42 09 AM" src="https://github.com/user-attachments/assets/eb6be7bd-95c9-40f5-ac21-727a3c918bf0" />
<img width="1470" height="71" alt="Screenshot 2026-02-21 at 9 26 07 AM" src="https://github.com/user-attachments/assets/48418fcd-b5cf-4957-94ec-f99a7ec017f0" />
<img width="1469" height="448" alt="Screenshot 2026-02-21 at 9 40 52 AM" src="https://github.com/user-attachments/assets/ddd9741c-2211-48ef-9773-2489b189fa73" />
<img width="1465" height="392" alt="Screenshot 2026-02-21 at 9 42 24 AM" src="https://github.com/user-attachments/assets/4f076a0c-912f-46ae-9bd2-bb301909ac1b" />
<img width="1469" height="322" alt="Screenshot 2026-02-21 at 9 43 25 AM" src="https://github.com/user-attachments/assets/849fc437-4f38-46b1-9e2b-c02a0a5c45e5" />
<img width="1463" height="404" alt="Screenshot 2026-02-21 at 9 43 56 AM" src="https://github.com/user-attachments/assets/d1cba5e8-216f-4adc-872a-caba6fbd27fe" />
<img width="1461" height="361" alt="Screenshot 2026-02-21 at 9 44 37 AM" src="https://github.com/user-attachments/assets/54aa8634-92b9-4381-8811-809a400fb4e5" />
<img width="1468" height="318" alt="Screenshot 2026-02-21 at 9 46 03 AM" src="https://github.com/user-attachments/assets/a2b140e3-a305-49e8-a3c5-c4730ce53d2d" />
<img width="1470" height="256" alt="Screenshot 2026-02-21 at 9 52 50 AM" src="https://github.com/user-attachments/assets/cb0407d2-6bb5-4a2a-9cd1-bf5ba4516b15" />
<img width="1468" height="875" alt="Screenshot 2026-02-21 at 9 53 38 AM" src="https://github.com/user-attachments/assets/e6e7ac7d-f2ed-4b11-8d2e-de4e4d98df64" />
<img width="1466" height="57" alt="Screenshot 2026-02-21 at 9 27 48 AM" src="https://github.com/user-attachments/assets/172aee6b-3b11-4c2e-92ae-e50cf2038820" />
<img width="1468" height="474" alt="Screenshot 2026-02-21 at 9 26 30 AM" src="https://github.com/user-attachments/assets/4271f5de-4263-4481-ac41-4649c45c7077" />
<img width="1470" height="335" alt="Screenshot 2026-02-21 at 9 38 25 AM" src="https://github.com/user-attachments/assets/3e465d8d-13c7-45df-a518-618737eb96b9" />

