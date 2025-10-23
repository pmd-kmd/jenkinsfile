pipeline {
    agent any

    environment {
        FRONTEND_REPO = 'https://github.com/pmd-kmd/test_frontend.git'
        BACKEND_REPO  = 'https://github.com/pmd-kmd/backend.git'
    }

    stages {
        stage('Clone Repositories') {
            steps {
                echo "Cloning frontend and backend repositories..."
                dir('frontend') {
                    git branch: 'main', url: "${FRONTEND_REPO}"
                }
                dir('backend') {
                    git branch: 'master', url: "${BACKEND_REPO}"
                }
            }
        }

        stage('Build Backend Docker Image') {
            steps {
                echo "Building backend Docker image..."
                dir('backend') {
                    sh 'docker build -t my-backend-app .'
                }
            }
        }

        stage('Build Frontend Docker Image') {
            steps {
                echo "Building frontend Docker image..."
                dir('frontend') {
                    sh 'docker build -t my-frontend-app ./frontend'
                }
            }
        }

        stage('Run Containers with Docker Compose') {
            steps {
                echo "Starting containers..."
                writeFile file: 'docker-compose.yml', text: """
                services:
                  backend:
                    image: my-backend-app
                    ports:
                      - "8080:8080"
                  frontend:
                    image: my-frontend-app
                    ports:
                      - "4200:80"
                    depends_on:
                      - backend
                """
                sh 'docker-compose up -d'
            }
        }
    }

    post {
        success {
            echo 'Build and deployment successful!'
            sh 'docker ps'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
