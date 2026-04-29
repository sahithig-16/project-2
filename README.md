# project-2
# 🚀 End-to-End DevOps Project – React Tetris on Kubernetes

## 📌 Project Overview

This project demonstrates a complete **end-to-end DevOps pipeline** for deploying a React-based Tetris game application. It integrates CI/CD, security scanning, containerization, and GitOps-based Kubernetes deployment.

---

## 🧠 Architecture Overview

The project follows a modern DevOps workflow:

1. Developer pushes code to GitHub
2. Jenkins triggers CI/CD pipeline
3. Code quality analysis using SonarQube
4. Security scanning using OWASP Dependency Check
5. Application is containerized using Docker
6. Docker image is pushed to Docker Hub
7. Argo CD monitors Git repository and deploys to Kubernetes
8. Application runs on Kubernetes (EKS)

---

## ⚙️ Technologies Used

* **Frontend:** React (Tetris Game)
* **CI/CD:** Jenkins
* **Code Quality:** SonarQube
* **Security:** OWASP Dependency Check
* **Containerization:** Docker
* **Orchestration:** Kubernetes
* **GitOps:** Argo CD
* **Infrastructure:** Terraform (EKS Setup)
* **Cloud:** AWS

---

## 📁 Project Structure

```id="g3jz71"
├── src/                     # React application
├── public/                  # Static files
├── Dockerfile               # Container build
├── deployment-service.yml   # Kubernetes deployment + service
├── Jenkins-CICD/            # CI/CD pipeline configuration
├── Jenkins-terraform/       # Infra provisioning setup
├── Eks-terraform/           # EKS cluster setup
├── images/                  # Screenshots
```

---

## 🔁 CI/CD Pipeline Flow

### 1. Code Checkout

* Jenkins pulls code from GitHub

### 2. SonarQube Analysis

* Static code analysis for bugs and vulnerabilities

### 3. OWASP Scan

* Dependency vulnerability scanning

### 4. Build Stage

* Application dependencies installed
* React app built

### 5. Docker Build & Push

```bash
docker build -t <image> .
docker push <image>
```

### 6. Image Update (GitOps)

* Deployment YAML updated with new image
* Changes pushed to GitHub

### 7. Argo CD Deployment

* Argo CD detects changes
* Automatically deploys to Kubernetes cluster

---

## ☸️ Kubernetes Deployment

* Deployment ensures application availability
* Service exposes application to users

Apply resources:

```bash
kubectl apply -f deployment-service.yml
```

---

## 🔐 Security & Quality

* SonarQube ensures code quality
* OWASP scans dependencies for vulnerabilities

---

## ☁️ Infrastructure (EKS)

* Kubernetes cluster provisioned using Terraform
* Infrastructure is automated and version-controlled

---

## 🔍 Troubleshooting

```id="0wjjb1"
kubectl get pods
kubectl describe pod <pod>
kubectl logs <pod>
docker logs <container>
```

---

## 🎯 Key Features

* End-to-end CI/CD pipeline
* Automated deployment using GitOps
* Integrated security scanning
* Infrastructure as Code using Terraform
* Scalable Kubernetes deployment

---

## 🚀 Future Improvements

* Add Ingress for routing
* Implement HPA (autoscaling)
* Add monitoring (Prometheus & Grafana)
* Use Helm charts

---
DevOps Engineer | AWS | Kubernetes | Docker | CI/CD

---
