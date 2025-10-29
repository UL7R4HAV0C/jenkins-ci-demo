pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = 'dockerhub_token'   // Jenkins credentials ID
        DOCKERHUB_USERNAME = 'your_dockerhub_username' // 🔹 Change this
        IMAGE_NAME = 'jenkins-ci-demo'
    }

    stages {
        stage('Checkout') {
            steps {
                echo '📦 Checking out source code...'
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                echo '🐳 Building Docker image...'
                bat "docker build -t %DOCKERHUB_USERNAME%/%IMAGE_NAME% ."
            }
        }

        stage('Push to Docker Hub') {
            steps {
                echo '🚀 Pushing Docker image to Docker Hub...'
                withCredentials([string(credentialsId: "${DOCKERHUB_CREDENTIALS}", variable: 'DOCKER_TOKEN')]) {
                    bat """
                    docker login -u %DOCKERHUB_USERNAME% -p %DOCKER_TOKEN%
                    docker push %DOCKERHUB_USERNAME%/%IMAGE_NAME%
                    """
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                echo '⚙️ Deploying to Kubernetes...'
                bat "kubectl apply -f deployment.yaml"
                bat "kubectl apply -f service.yaml"
                bat "kubectl get pods"
                bat "kubectl get svc"
            }
        }
    }

    post {
        success {
            echo '✅ Deployment Successful!'
        }
        failure {
            echo '❌ Pipeline failed! Check logs.'
        }
    }
}
