pipeline {
  agent any
  environment {
    IMAGE = "ul7r4hav0c/jenkins-ci-demo"
    TAG = "${env.BUILD_NUMBER}"
    DOCKER_CRED = 'dockerhub-creds'
    KUBE_CRED = 'kubeconfig-file'
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build Docker Image') {
      steps {
        sh "docker build -t ${IMAGE}:${TAG} ."
      }
    }

    stage('Push to Docker Hub') {
      steps {
        withCredentials([usernamePassword(credentialsId: DOCKER_CRED, usernameVariable: 'USER', passwordVariable: 'PASS')]) {
          sh "echo $PASS | docker login -u $USER --password-stdin"
          sh "docker push ${IMAGE}:${TAG}"
        }
      }
    }

    stage('Deploy to Kubernetes') {
      steps {
        withCredentials([file(credentialsId: KUBE_CRED, variable: 'KUBECONFIG_FILE')]) {
          sh 'mkdir -p $HOME/.kube'
          sh 'cp $KUBECONFIG_FILE $HOME/.kube/config'
          // update image tag dynamically in YAML
          sh "sed -i 's|ul7r4hav0c/jenkins-ci-demo:latest|${IMAGE}:${TAG}|g' deployment.yaml"
          sh "kubectl apply -f deployment.yaml"
          sh "kubectl apply -f service.yaml"
          sh "kubectl rollout status deployment/jenkins-ci-demo --timeout=90s"
        }
      }
    }
  }

  post {
    success {
      echo "✅ Successfully deployed ${IMAGE}:${TAG} to Kubernetes!"
    }
    failure {
      echo "❌ Pipeline failed! Check logs."
    }
  }
}
