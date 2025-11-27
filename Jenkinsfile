pipeline {
    agent any

    environment {
        DOCKERHUB = credentials('docker-hub')
        IMAGE_NAME = "<your-dockerhub-username>/jenkins-cicd-demo"
        DEPLOY_HOST = "3.110.56.167"         // e.g. 3.110.x.x
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
                sh 'npm install'
                // For Java: sh 'mvn clean install'
            }
        }

        stage('Test') {
            steps {
                echo "Running tests"
                sh 'npm test'
                // For Java: sh 'mvn test'
            }
        }

        stage('Package') {
            steps {
                echo "Packaging app"
                sh 'npm run build || echo "No build step needed"'
                // For Java: sh 'mvn package'
            }
        }

        stage('Docker Build') {
            steps {
                echo "Building Docker image"
                sh """
                   docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} .
                   docker tag ${IMAGE_NAME}:${BUILD_NUMBER} ${IMAGE_NAME}:latest
                   """
            }
        }

        stage('Docker Push') {
            steps {
                echo "Logging into Docker Hub & pushing image"
                sh """
                   echo ${DOCKERHUB_PSW} | docker login -u ${DOCKERHUB_USR} --password-stdin
                   docker push ${IMAGE_NAME}:${BUILD_NUMBER}
                   docker push ${IMAGE_NAME}:latest
                   """
            }
        }

        stage('Deploy') {
            steps {
                echo "Deploying to remote server"
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
            echo "Pipeline completed successfully!"
        }
        failure {
            echo "Pipeline failed. Check above logs."
        }
    }
}
