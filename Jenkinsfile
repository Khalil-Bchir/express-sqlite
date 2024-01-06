pipeline {
    agent any

    environment {
        // Add Docker Hub credentials as authentication variables
        DOCKERHUB_CREDENTIALS = credentials('dh_cred')
    }

    triggers {
        pollSCM('*/5 * * * *') // Poll SCM every 5 minutes
    }

    stages {
        stage('Checkout') {
            agent any
            steps {
                checkout scm
            }
        }

        stage('Init') {
            steps {
                // Authenticate with Docker Hub
                sh "echo \${DOCKERHUB_CREDENTIALS_PSW} | docker login -u \${DOCKERHUB_CREDENTIALS_USR} --password-stdin"
            }
        }

        stage('Build') {
            steps {
                // Build Docker image and tag it
                sh "docker build -t \${DOCKERHUB_CREDENTIALS_USR}/express-sqlite-app:\$BUILD_ID ."
            }
        }

        stage('Deliver') {
            steps {
                // Push Docker image to Docker Hub
                sh "docker push \${DOCKERHUB_CREDENTIALS_USR}/express-sqlite-app:\$BUILD_ID"
            }
        }

        stage('Cleanup') {
            steps {
                // Remove local Docker image and logout from Docker Hub
                sh "docker rmi \${DOCKERHUB_CREDENTIALS_USR}/express-sqlite-app:\$BUILD_ID"
                sh "docker logout"
            }
        }
    }
}
