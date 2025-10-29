pipeline {
    agent any

    environment {
        DOCKER_HUB_REPO = 'ul7r4hav0c/jenkins-ci-demo'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main',
                    credentialsId: 'github',
                    url: 'https://github.com/ul7r4hav0c/jenkins-ci-demo.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${DOCKER_HUB_REPO}:latest")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub') {
                        dockerImage.push()
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    kubectlApply = bat(returnStatus: true, script: '''
                        kubectl apply -f deployment.yaml
                        kubectl apply -f service.yaml
                    ''')
                    if (kubectlApply != 0) {
                        error("Kubernetes deployment failed!")
                    }
                }
            }
        }
    }

    post {
        success {
            echo '✅ Deployment successful!'
        }
        failure {
            echo '❌ Deployment failed. Check logs!'
        }
    }
}
