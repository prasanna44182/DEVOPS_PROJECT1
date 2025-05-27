pipeline {
    agent any

    environment {
        APP_NAME = 'notes-cicd-app'
        IMAGE_TAG = 'v1'
    }

    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Clone Repository') {
            steps {
                echo 'Cloning repository...'
                // Already done by checkout usually
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                sh "docker build -t $APP_NAME:$IMAGE_TAG ."
            }
        }

        stage('Stop and Remove Previous Container') {
            steps {
                sh '''
                    docker stop $APP_NAME || true
                    docker rm $APP_NAME || true
                '''
            }
        }

        stage('Run Docker Container') {
            steps {
                sh "docker run -d -p 8000:8000 --name $APP_NAME $APP_NAME:$IMAGE_TAG"
            }
        }
    }
}
