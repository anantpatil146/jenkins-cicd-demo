pipeline {
    agent any

    environment {
        DOCKERHUB = credentials('docker-hub')
        IMAGE_NAME = "anantpatil146/myapp-image"
        DEPLOY_HOST = "3.110.56.167"
        DEPLOY_USER = "ubuntu"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo "Installing dependencies"
                bat 'npm install'
            }
        }

        stage('Test') {
            steps {
                echo "Running tests"
                bat 'npm test'
            }
        }

        stage('Package') {
            steps {
                echo "Packaging app"
                bat 'npm run build || echo No build step needed'
            }
        }

        stage('Docker Build') {
            steps {
                echo "Building Docker image"
                bat "docker build -t %IMAGE_NAME%:%BUILD_NUMBER% ."
                bat "docker tag %IMAGE_NAME%:%BUILD_NUMBER% %IMAGE_NAME%:latest"
            }
        }

        stage('Docker Push') {
            steps {
                echo "Logging into Docker Hub & pushing image"
                bat "echo %DOCKERHUB_PSW% | docker login -u %DOCKERHUB_USR% --password-stdin"
                bat "docker push %IMAGE_NAME%:%BUILD_NUMBER%"
                bat "docker push %IMAGE_NAME%:latest"
            }
        }

        stage('Deploy') {
            steps {
                echo "Deploying to remote server"
                sshagent (credentials: ['ec2-ssh']) {
                    bat """
                    ssh -o StrictHostKeyChecking=no %DEPLOY_USER%@%DEPLOY_HOST% "docker pull %IMAGE_NAME%:latest && docker rm -f cicd-demo || true && docker run -d --name cicd-demo -p 80:3000 %IMAGE_NAME%:latest"
                    """
                }
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully!"
        }
        failure {
            echo "Pipeline failed. Check above logs."
        }
    }
}
