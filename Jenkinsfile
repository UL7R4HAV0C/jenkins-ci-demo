pipeline {
    agent any

    environment {
        // 🌐 Credentials and image info
        DOCKERHUB_CREDENTIALS = 'dockerhub'       // Jenkins DockerHub credentials ID
        DOCKERHUB_USERNAME = 'ul7r4hav0c'         // Your DockerHub username
        IMAGE_NAME = 'jenkins-ci-demo'            // Docker image name
        TAG = 'latest'                            // Optional tag
    }

    stages {

        stage('Checkout') {
            steps {
                echo '📦 Checking out source code...'
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/main']],              // branch to build
                    userRemoteConfigs: [[
                        url: 'https://github.com/ul7r4hav0c/jenkins-ci-demo.git', // 🔹 your repo
                        credentialsId: 'github'               // GitHub credentials ID
                    ]]
                ])
            }
        }

        stage('Build Docker Image') {
            steps {
                echo '🐳 Building Docker image...'
                bat """
                docker build -t %DOCKERHUB_USERNAME%/%IMAGE_NAME%:%TAG% .
                """
            }
        }

        stage('Push to Docker Hub') {
            steps {
                echo '🚀 Pushing Docker image to Docker Hub...'
                withCredentials([usernamePassword(credentialsId: "${DOCKERHUB_CREDENTIALS}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    bat """
                    docker login -u %DOCKER_USER% -p %DOCKER_PASS%
                    docker push %DOCKERHUB_USERNAME%/%IMAGE_NAME%:%TAG%
                    docker logout
                    """
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                echo '⚙️ Deploying to Kubernetes...'

                // ✅ Use the kubeconfig file stored in Jenkins credentials
                withCredentials([file(credentialsId: 'minikube-config', variable: 'KUBECONFIG')]) {
                    bat """
                    kubectl apply -f deployment.yaml
                    kubectl apply -f service.yaml
                    kubectl get pods
                    kubectl get svc
                    """
                }
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
