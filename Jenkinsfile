pipeline {
    agent any

    environment {
        APP_NAME = "notes-cicd-app"
        IMAGE_TAG = "v1"
        PROJECT_DIR = "NOTES_CICD"
    }

    stages {
        stage('Checkout Code') {
            steps {
                echo 'Cloning repository...'
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                dir("${env.PROJECT_DIR}") {
                    echo 'Building Docker image...'
                    sh "docker build -t ${APP_NAME}:${IMAGE_TAG} ."
                }
            }
        }

        stage('Stop and Remove Previous Container') {
            steps {
                echo 'Stopping and removing old container if exists...'
                sh """
                docker stop ${APP_NAME} || true
                docker rm ${APP_NAME} || true
                """
            }
        }

        stage('Run Docker Container') {
            steps {
                echo 'Running new container...'
                sh """
                docker run -d -p 8000:8000 --name ${APP_NAME} ${APP_NAME}:${IMAGE_TAG} \
                python manage.py runserver 0.0.0.0:8000
                """
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution completed.'
        }
    }
}
