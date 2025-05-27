Full-Scale DevOps Project: Django App CI/CD on AWS with Docker & Jenkins

1. Introduction
This document details a comprehensive DevOps project that involves building, dockerizing, deploying, and automating the CI/CD pipeline for a Django web application. This project is a complete hands-on implementation hosted on an AWS EC2 instance, leveraging Docker, Jenkins, and GitHub.

2. Prerequisites
- A GitHub account
- AWS account with access to EC2
- Jenkins (Installed on Docker)
- Docker (Installed and configured)
- Basic knowledge of Django, Linux, Git, and shell commands

3. Creating the Django Application
django-admin startproject NOTES_CICD
cd NOTES_CICD
python manage.py startapp notes

4. Dockerizing the Application
Create a `Dockerfile` inside your Django project directory:
FROM python:3.12-slim
ENV PYTHONDONTWRITEBYTECODE 1
ENV PYTHONUNBUFFERED 1
WORKDIR /app
COPY . /app/
RUN pip install --upgrade pip
RUN pip install -r requirements.txt

5. Docker Commands
docker build -t notes-cicd-app .
docker run -d -p 8000:8000 notes-cicd-app

6. Configure ALLOWED_HOSTS
ALLOWED_HOSTS = []

7. Running Django Publicly
python manage.py runserver 0.0.0.0:8000

8. Deploy Dockerized App on AWS EC2
sudo apt update
sudo apt install docker.io -y
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker ubuntu
docker run hello-world

9. Push Project to GitHub
git init
git remote add origin https://github.com/YOUR_USERNAME/DEVOPS_PROJECT1.git
git add .
git commit -m "Initial commit"
git push -u origin main

10. Setup Jenkins Using Docker
docker run -d \
  -p 8080:8080 -p 50000:50000 \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v jenkins_home:/var/jenkins_home \
  --name jenkins \
  jenkins/jenkins:lts
11. Jenkins Initial Setup
- Access Jenkins on http://<ec2-ip>:8080
- Retrieve admin password from: `/var/jenkins_home/secrets/initialAdminPassword`
- Install recommended plugins
- Create admin user

12. Jenkins Pipeline Configuration
Sample `Jenkinsfile`:
pipeline {
    agent any
    stages {
        stage('Clone') {
            steps {
                git 'https://github.com/YOUR_USERNAME/DEVOPS_PROJECT1.git'
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

