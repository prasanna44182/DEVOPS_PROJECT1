
# 🚀 Full-Scale DevOps Project: Django App CI/CD on AWS with Docker & Jenkins

## 📌 Introduction
This is a comprehensive guide to building, dockerizing, deploying, and automating a CI/CD pipeline for a Django web application. It covers everything **end-to-end** from initial development to deployment using Docker and Jenkins on an **AWS EC2** instance. This document assumes **zero skipped steps** and captures **every single command** and detail.

---

## ✅ Prerequisites

- GitHub account (with or without PAT)
- AWS account (free tier or Educate)
- EC2 Instance (Ubuntu 20.04 or later)
- Jenkins (Dockerized on EC2)
- Docker (installed on EC2 and Mac)
- Git & GitHub CLI or Desktop App
- Basic knowledge of:
  - Django development
  - Docker containerization
  - Linux shell commands
  - CI/CD concepts

---

## 🛠️ PART 1: Create Django App Locally

```bash
# Create and activate a virtual environment
python3 -m venv venv
source venv/bin/activate

# Create Django Project
django-admin startproject NOTES_CICD
cd NOTES_CICD

# Create Django App
python manage.py startapp notes

# Add 'notes' to INSTALLED_APPS in settings.py
# Run migrations and start the app to test
python manage.py migrate
python manage.py runserver
```

---

## 🐳 PART 2: Dockerize the Django App

### 1. Create Dockerfile inside NOTES_CICD

```Dockerfile
FROM python:3.12-slim

ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

WORKDIR /app

COPY . /app/
RUN pip install --upgrade pip
RUN pip install -r requirements.txt

EXPOSE 8000
CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]
```

### 2. Build Docker Image

```bash
docker build -t notes-cicd-app .
```

### 3. Run the Docker Container

```bash
docker run -d -p 8000:8000 notes-cicd-app
```

If `ALLOWED_HOSTS` error appears:

```python
# settings.py
ALLOWED_HOSTS = ['<your-ec2-ip>', 'localhost', '127.0.0.1']
```

---

## ☁️ PART 3: AWS EC2 Instance Setup

```bash
# Launch EC2 instance with Ubuntu 20.04
# SSH into EC2 using EC2 Instance Connect or .pem file

sudo apt update && sudo apt upgrade -y
sudo apt install docker.io -y

sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker ubuntu
newgrp docker

docker run hello-world
```

Transfer files to EC2 or git clone from GitHub:

```bash
git clone https://github.com/your_username/DEVOPS_PROJECT1.git
cd DEVOPS_PROJECT1/NOTES_CICD
```

Then build and run:

```bash
docker build -t notes-cicd-app .
docker run -d -p 8000:8000 notes-cicd-app
```

---

## 🐙 PART 4: Push Project to GitHub

```bash
git init
git remote add origin https://github.com/<username>/DEVOPS_PROJECT1.git
git add .
git commit -m "Initial commit"
git branch -M main
git push -u origin main
```

---

## 🔧 PART 5: Setup Jenkins on EC2 via Docker

```bash
docker run -d   -p 8080:8080 -p 50000:50000   -v /var/run/docker.sock:/var/run/docker.sock   -v jenkins_home:/var/jenkins_home   --name jenkins   jenkins/jenkins:lts
```

- Access Jenkins at `http://<EC2-PUBLIC-IP>:8080`
- Get admin password:

```bash
sudo cat /var/jenkins_home/secrets/initialAdminPassword
```

- Install plugins
- Create admin user

---

## 🔁 PART 6: Configure Jenkins Pipeline

### Create `Jenkinsfile` (in root or `NOTES_CICD/`):

```groovy
pipeline {
    agent any

    stages {
        stage('Clone') {
            steps {
                git 'https://github.com/<username>/DEVOPS_PROJECT1.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                dir('NOTES_CICD') {
                    sh 'docker build -t notes-cicd-app:v1 .'
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                sh 'docker run -d -p 8000:8000 notes-cicd-app:v1'
            }
        }
    }
}
```

---

## 💡 Common Errors + Fixes

- **ALLOWED_HOSTS Error**: Add EC2 public IP to `settings.py`
- **Port Already in Use**: Run `docker ps` + `docker stop <container-id>`
- **Docker Permission Denied in Jenkins**: Add Jenkins to Docker group or run Jenkins with docker.sock volume
- **GitHub Authentication**: Use SSH or make repo public

---

## 📎 Final URL

Access your app at: `http://<EC2-PUBLIC-IP>:8000`

---

## 🎯 Summary

| Component | Tool / Service |
|----------|----------------|
| SCM      | GitHub         |
| CI/CD    | Jenkins        |
| Containerization | Docker |
| Hosting  | AWS EC2        |
| Web Framework | Django     |
| Deployment | Manual & Jenkins Pipeline |

---

**You've completed a full CI/CD pipeline deployment with Docker + Jenkins + AWS + Django.** 💪🔥

