// ================================
// Jenkins Declarative Pipeline Template
// ONLY COMMENTS - DO NOT EXECUTE ANY PIPELINE TASKS
// ================================

pipeline {
    agent any    // Run pipeline on any available Jenkins agent/node

    environment {
        // Docker Hub credentials stored in Jenkins credentials
        // DOCKERHUB = credentials('docker-hub')

        // Name of the Docker image to build and push
        // IMAGE_NAME = "username/project-image"

        // EC2 Server details for deployment
        // DEPLOY_HOST = "EC2-Public-IP"
        // DEPLOY_USER = "ubuntu"
    }

    stages {

        stage('Checkout Code') {
            steps {
                // Checkout source code from Git repository
                // Normally uses: checkout scm
            }
        }

        stage('Build Dependencies') {
            steps {
                // Install dependencies such as npm, mvn, gradle, etc.
                // Example: sh 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                // Run unit or integration tests
                // Example: sh 'npm test'
            }
        }

        stage('Build Docker Image') {
            steps {
                // Build Docker image using Dockerfile
                // Example: docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} .
            }
        }

        stage('Push Docker Image') {
            steps {
                // Login to Docker Hub and push image
                // Example: docker push image-name
            }
        }

        stage('Deploy to EC2') {
            steps {
                // Connect to EC2 via SSH and deploy container
                // Example: ssh to server and restart container
            }
        }
    }

    post {
        success {
            // Actions to run when pipeline succeeds
            // Example: send success notification
        }
        failure {
            // Actions to run when pipeline fails
            // Example: send failure notification
        }
    }
}
