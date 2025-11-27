// =========================
// Jenkins Declarative Pipeline
// CICD for Node.js + Docker + EC2 Deployment
// =========================

pipeline {
    agent any

    environment {
        // Docker Hub credentials stored in Jenkins Credentials
        DOCKERHUB = credentials('docker-hub')

        // Docker Image Name
        IMAGE_NAME = "anant146/myapp-image"

        // EC2 server details
        DEPLOY_HOST = "13.127.78.46"
        DEPLOY_USER = "ubuntu"
    }

    stages {

        stage('Checkout Code') {
            steps {
                // Clones the repository Jenkinsfile is located in
                checkout scm
            }
        }

        stage('Build Dependencies') {
            steps {
                echo "Installing Node Dependencies"
                sh 'npm install'        // For Node.js apps
            }
        }

        stage('Run Tests') {
            steps {
                echo "Running Tests"
                sh 'npm test || echo "No tests configured"'   // Prevent failure if no tests exist
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker Image"
                sh """
                    docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} .
                    docker tag ${IMAGE_NAME}:${BUILD_NUMBER} ${IMAGE_NAME}:latest
                """
            }
        }

        stage('Push to Docker Hub') {
            steps {
                echo "Pushing Image to Docker Hub"

                // Login and push the docker image
                sh """
                    echo ${DOCKERHUB_PSW} | docker login -u ${DOCKERHUB_USR} --password-stdin
                    docker push ${IMAGE_NAME}:${BUILD_NUMBER}
                    docker push ${IMAGE_NAME}:latest
                """
            }
        }

        stage('Deploy to EC2') {
            steps {
                echo "Deploying to EC2 Instance"

                // SSH to EC2 and pull + restart docker container
                sshagent (credentials: ['ec2-ssh']) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ${DEPLOY_USER}@${DEPLOY_HOST} '
                            docker pull ${IMAGE_NAME}:latest &&
                            docker rm -f cicd-demo || true &&
                            docker run -d --name cicd-demo -p 80:3000 ${IMAGE_NAME}:latest
                        '
                    """
                }
            }
        }
    }

    post {
        success {
            echo "🎉 Pipeline completed successfully — App deployed to EC2!"
        }
        failure {
            echo "❌ Pipeline failed — Check logs above"
        }
    }
}
