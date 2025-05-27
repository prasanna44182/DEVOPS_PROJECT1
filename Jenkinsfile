pipeline {
    agent any

    environment {
        APP_NAME = "notes-cicd-app"
        IMAGE_TAG = "latest"
    }

    stages {
        stage('Clone Repository') {
            steps {
                echo 'Cloning GitHub repository...'
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                sh 'docker build -t $APP_NAME:$IMAGE_TAG .'
            }
        }

        stage('Stop and Remove Previous Container') {
            steps {
                echo 'Stopping old container if exists...'
                sh '''
                docker stop $APP_NAME || true
                docker rm $APP_NAME || true
                '''
            }
        }

        stage('Run Docker Container') {
            steps {
                echo 'Running container...'
                sh '''
                docker run -d --name $APP_NAME -p 8000:8000 $APP_NAME:$IMAGE_TAG python manage.py runserver 0.0.0.0:8000
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Deployment successful!'
        }
        failure {
            echo '❌ Deployment failed.'
        }
    }
}
