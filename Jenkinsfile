pipeline {
    agent any   // Use any available agent or node

    stages {

        stage('Checkout') {
            steps {
                echo "✔ Checking out source code"
                checkout scm   // Fetch code from repository
            }
        }

        stage('Install Dependencies') {
            steps {
                echo "✔ Installing project dependencies"
                // For Node.js apps:
                bat 'npm install'  // Use sh 'npm install' for Linux agents
            }
        }

        stage('Build') {
            steps {
                echo "✔ Building Application"
                // For Node.js build step (or Maven/Gradle etc based on project)
                bat 'npm run build || echo "No build step required"'  // use sh on Linux
            }
        }
    }

    post {
        success {
            echo "🎉 Build Successful"
        }
        failure {
            echo "❌ Build Failed - Check logs"
        }
    }
}
