# CI/CD Pipeline Deployment to EC2 (GitHub Actions)

## Project Overview

This project demonstrates a **complete CI/CD pipeline** where code is automatically tested and deployed to an AWS EC2 server.

Whenever code is pushed:

* Tests are executed (CI)
* Code is deployed to EC2 (CD)
* Application is restarted automatically

---

## Project Goal

* Automate build and deployment
* Deploy application to EC2
* Ensure code quality using tests
* Implement secure DevOps practices

---

## Architecture

Developer → GitHub → GitHub Actions → EC2 Server → Live App

---

## Tech Stack

* Python (Flask)
* GitHub Actions
* AWS EC2
* SSH

---

## Implementation Steps

### 1️) Setup EC2 Server

```bash
sudo apt update
sudo apt install python3-pip -y
pip3 install flask
```

Create app:

```python id="code1"
from flask import Flask
app = Flask(__name__)

@app.route("/")
def home():
    return "Hello from EC2 🚀"

app.run(host="0.0.0.0", port=5000)
```

Run:

```bash id="code2"
python3 app.py
```
 Access:

```id="code3"
http://<EC2-PUBLIC-IP>:5000
```

---

### 2️) Setup SSH Access

```bash id="code4"
ssh-keygen
ssh-copy-id ubuntu@your-ec2-ip
```

---

### 3️) Add GitHub Secrets

Go to:
GitHub → Settings → Secrets → Actions

Add:

* `EC2_HOST`
* `EC2_USER`
* `EC2_SSH_KEY`

---

### 4️) GitHub Actions Workflow

```yaml id="code5"
name: CI-CD Pipeline

on:
  push:
    branches: ["main"]

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout
      uses: actions/checkout@v3

    - name: Setup Python
      uses: actions/setup-python@v4
      with:
        python-version: "3.10"

    - name: Install deps
      run: pip install -r requirements.txt

    - name: Run tests
      run: pytest

    - name: Deploy to EC2
      uses: appleboy/ssh-action@v1.0.0
      with:
        host: ${{ secrets.EC2_HOST }}
        username: ${{ secrets.EC2_USER }}
        key: ${{ secrets.EC2_SSH_KEY }}
        script: |
          cd /home/ubuntu/app || git clone https://github.com/YOUR_USERNAME/YOUR_REPO.git app
          cd app
          git pull
          pip3 install -r requirements.txt
          pkill -f app.py || true
          nohup python3 app.py > output.log 2>&1 &
```

---

## CI/CD Flow

1. Developer pushes code
2. GitHub Actions triggers pipeline
3. Dependencies installed
4. Tests executed
5. SSH into EC2
6. Pull latest code
7. Restart application

---

## Key Learnings

* CI/CD pipeline design
* Secure deployment using secrets
* Remote server automation
* Application lifecycle management
---

## Common Mistakes

* Hardcoding credentials
* No testing before deployment
* Application stopping after SSH
* No logging mechanism

---

## Author

Dhinakar M
