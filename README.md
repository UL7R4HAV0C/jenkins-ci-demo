# CI/CD Pipeline: Dockerized Flask App Deployment on Kubernetes using Jenkins

## Overview

This project demonstrates a complete CI/CD pipeline to automatically build, push, and deploy a Flask application using:
- Jenkins for automation  
- Docker for containerization  
- Kubernetes (Minikube) for deployment  
- Docker Hub for image hosting  
- GitHub for version control  

---

## Project Structure
```
jenkins-ci-demo/
├── app.py
├── Dockerfile
├── Jenkinsfile
├── deployment.yaml
├── service.yaml
├── requirements.txt
└── README.md
```

---

## Tools & Technologies

- Python 3.9 / Flask  
- Docker & Docker Hub  
- Jenkins  
- Kubernetes (Minikube)  
- GitHub  

---

## Setup Overview

### 1. Repository Setup
- Create a GitHub repository `jenkins-ci-demo` and push all files.  
- Ensure the repository contains Dockerfile, Jenkinsfile, and Kubernetes YAMLs.

### 2. Minikube Setup
- Install Minikube and start the cluster:
```bash
  minikube start
  kubectl get nodes
```

- Locate the kubeconfig file (usually at `C:\Users\<username>\.kube\config`) — this will be uploaded to Jenkins later.

### 3. Jenkins Configuration

#### Required Plugins
- Pipeline
- Git
- Docker Pipeline
- Kubernetes CLI
- Credentials Binding

#### Add Credentials
Navigate to **Dashboard → Manage Jenkins → Credentials → Global → Add Credentials** and create:

| Type | ID | Description |
|------|-------|-------------|
| Secret Text | `dockerhub-token` | Docker Hub Access Token |
| Username/Password | `github-credentials` | GitHub Authentication (optional) |
| Secret File | `kubeconfig` | Upload Minikube kubeconfig file |

---

## Pipeline Flow (Jenkinsfile Summary)

The Jenkins pipeline automates the following stages:

1. **Checkout** – Pull code from GitHub
2. **Build** – Build Docker image
3. **Push** – Push image to Docker Hub
4. **Deploy** – Apply Kubernetes manifests using Minikube config

Each stage uses the credentials securely stored in Jenkins.

---

## Deployment Verification

After a successful Jenkins build:
```bash
kubectl get pods
kubectl get svc
```

Access the running Flask app:
```bash
minikube service jenkins-ci-demo-service
```

---

## Summary

This setup enables:
- Continuous Integration (CI) via Jenkins and GitHub
- Continuous Deployment (CD) to Kubernetes
- Automated Docker image management and rollout

---

## Author

**Pranshu Verma**  
GitHub: [@UL7R4HAV0C](https://github.com/UL7R4HAV0C)  
*CI/CD Pipeline using Jenkins, Docker, and Kubernetes*
