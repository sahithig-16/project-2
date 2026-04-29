# 🚀 Kubernetes DevOps Project

> A production-grade Kubernetes setup with CI/CD via **ArgoCD**, and observability via **Prometheus** + **Grafana**.

![Kubernetes](https://img.shields.io/badge/Kubernetes-1.29+-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white)
![ArgoCD](https://img.shields.io/badge/ArgoCD-GitOps-EF7B4D?style=for-the-badge&logo=argo&logoColor=white)
![Prometheus](https://img.shields.io/badge/Prometheus-Monitoring-E6522C?style=for-the-badge&logo=prometheus&logoColor=white)
![Grafana](https://img.shields.io/badge/Grafana-Dashboards-F46800?style=for-the-badge&logo=grafana&logoColor=white)
![Jenkins](https://img.shields.io/badge/Jenkins-CI%2FCD-D24939?style=for-the-badge&logo=jenkins&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-Container-2496ED?style=for-the-badge&logo=docker&logoColor=white)

---

## 📋 Table of Contents

- [Project Overview](#-project-overview)
- [Architecture](#-architecture)
- [Tech Stack](#-tech-stack)
- [Prerequisites](#-prerequisites)
- [Project Structure](#-project-structure)
- [Getting Started](#-getting-started)
- [ArgoCD Setup (GitOps CD)](#-argocd-setup-gitops-cd)
- [Prometheus & Grafana Monitoring](#-prometheus--grafana-monitoring)
- [Kubernetes Manifests](#-kubernetes-manifests)
- [CI/CD Pipeline](#-cicd-pipeline)
- [Accessing Services](#-accessing-services)
- [Troubleshooting](#-troubleshooting)
- [Contributing](#-contributing)

---

## 🌐 Project Overview

This project demonstrates a **production-ready Kubernetes deployment** that follows modern DevOps best practices:

- **Containerized microservices** deployed on Kubernetes
- **Jenkins CI pipeline** for automated build, test, and Docker image push on every commit
- **GitOps-based continuous delivery** with ArgoCD (auto-syncs updated manifests to the cluster)
- **Full observability stack** using Prometheus (metrics collection) and Grafana (visualization)
- **Declarative infrastructure** — everything is version-controlled as YAML manifests

---

## 🏗 Architecture

```
                         ┌─────────────────────────────────────────┐
                         │             GitHub Repository            │
                         │  (Source Code + K8s Manifests)          │
                         └────────────────┬────────────────────────┘
                                          │  Git Push / Webhook Trigger
                                          ▼
                         ┌─────────────────────────────────────────┐
                         │           Jenkins CI Pipeline            │
                         │   Build → Test → Push Image to DockerHub │
                         └────────────────┬────────────────────────┘
                                          │  Update image tag in manifests + Git push
                                          ▼
┌───────────────────────────────────────────────────────────────────────┐
│                         Kubernetes Cluster                            │
│                                                                       │
│  ┌──────────────┐    ┌───────────────────────────────────────────┐   │
│  │   ArgoCD     │───▶│           Application Namespace           │   │
│  │  (GitOps CD) │    │  ┌─────────────┐   ┌──────────────────┐  │   │
│  └──────────────┘    │  │  Deployment │   │     Service      │  │   │
│                       │  │  (App Pods) │   │  (LoadBalancer)  │  │   │
│  ┌──────────────┐    │  └─────────────┘   └──────────────────┘  │   │
│  │  Prometheus  │    └───────────────────────────────────────────┘   │
│  │  (Metrics)   │◀─── Scrapes metrics from all pods/nodes            │
│  └──────┬───────┘                                                     │
│         │                                                             │
│  ┌──────▼───────┐                                                     │
│  │   Grafana    │  ◀── Visualizes Prometheus data via dashboards      │
│  │ (Dashboards) │                                                     │
│  └──────────────┘                                                     │
└───────────────────────────────────────────────────────────────────────┘
```

---

## 🛠 Tech Stack

| Tool | Purpose |
|------|---------|
| **Kubernetes** | Container orchestration |
| **Docker** | Containerization |
| **ArgoCD** | GitOps-based continuous delivery |
| **Prometheus** | Metrics collection & alerting |
| **Grafana** | Metrics visualization & dashboards |
| **Jenkins** | CI pipeline (build, test, push image) |
| **Helm** | Kubernetes package manager |

---

## ✅ Prerequisites

Make sure you have these installed before proceeding:

- [kubectl](https://kubernetes.io/docs/tasks/tools/) >= 1.27
- [Helm](https://helm.sh/docs/intro/install/) >= 3.x
- [Docker](https://docs.docker.com/get-docker/)
- A running Kubernetes cluster (Minikube / Kind / EKS / GKE / AKS)
- [Jenkins](https://www.jenkins.io/doc/book/installing/) >= 2.400 (with Docker + Git plugins)
- [ArgoCD CLI](https://argo-cd.readthedocs.io/en/stable/cli_installation/) (optional but recommended)

---

## 📁 Project Structure

```
k8s-project/
├── Jenkinsfile                     # Jenkins declarative pipeline
├── app/
│   ├── Dockerfile                  # Application Docker image
│   └── src/                        # Application source code
├── k8s/
│   ├── namespace.yaml              # Namespace definition
│   ├── deployment.yaml             # App Deployment manifest
│   ├── service.yaml                # App Service (ClusterIP/LoadBalancer)
│   ├── configmap.yaml              # App ConfigMap
│   └── ingress.yaml                # Ingress rules (optional)
├── argocd/
│   ├── install.yaml                # ArgoCD installation manifest
│   └── application.yaml            # ArgoCD Application CRD (points to k8s/)
├── monitoring/
│   ├── prometheus/
│   │   ├── values.yaml             # Helm values for Prometheus
│   │   └── alerting-rules.yaml     # Custom alert rules
│   └── grafana/
│       ├── values.yaml             # Helm values for Grafana
│       └── dashboards/
│           └── k8s-dashboard.json  # Custom Grafana dashboard
└── README.md
```

---

## 🚀 Getting Started

### 1. Clone the Repository

```bash
git clone https://github.com/devops0014/k8s-project.git
cd k8s-project
```

### 2. Set Up Your Kubernetes Cluster

```bash
# Option A: Minikube (local)
minikube start --cpus=4 --memory=8192

# Option B: Kind (local)
kind create cluster --name k8s-project

# Verify cluster is running
kubectl cluster-info
kubectl get nodes
```

### 3. Create Namespace

```bash
kubectl apply -f k8s/namespace.yaml
```

### 4. Deploy the Application

```bash
kubectl apply -f k8s/
kubectl get pods -n <your-namespace>
```

---

## 🔄 ArgoCD Setup (GitOps CD)

ArgoCD watches this Git repository and automatically syncs any changes to the cluster.

### Step 1 — Install ArgoCD

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Wait for pods to be ready
kubectl wait --for=condition=Ready pod --all -n argocd --timeout=120s
```

### Step 2 — Access ArgoCD UI

```bash
# Port-forward to access locally
kubectl port-forward svc/argocd-server -n argocd 8080:443

# Get initial admin password
kubectl -n argocd get secret argocd-initial-admin-secret \
  -o jsonpath="{.data.password}" | base64 -d && echo
```

Open your browser at: **https://localhost:8080**
- Username: `admin`
- Password: *(from the command above)*

### Step 3 — Connect ArgoCD to This Repository

```bash
# Login via CLI
argocd login localhost:8080 --username admin --password <password> --insecure

# Add your Git repo
argocd repo add https://github.com/devops0014/k8s-project.git
```

### Step 4 — Create the ArgoCD Application

Apply the ArgoCD Application manifest:

```bash
kubectl apply -f argocd/application.yaml
```

**`argocd/application.yaml`** — example content:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: k8s-project
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/devops0014/k8s-project.git
    targetRevision: HEAD
    path: k8s
  destination:
    server: https://kubernetes.default.svc
    namespace: default
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
    syncOptions:
      - CreateNamespace=true
```

### Step 5 — Verify Sync

```bash
argocd app get k8s-project
argocd app sync k8s-project   # manual sync if needed
```

✅ From now on, any push to the `k8s/` folder automatically triggers a deployment!

---

## 📊 Prometheus & Grafana Monitoring

### Install the kube-prometheus-stack (Helm)

This installs Prometheus, Grafana, and Alertmanager together.

```bash
# Add Helm repo
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update

# Create monitoring namespace
kubectl create namespace monitoring

# Install the stack
helm install kube-prometheus-stack prometheus-community/kube-prometheus-stack \
  --namespace monitoring \
  --values monitoring/prometheus/values.yaml
```

**`monitoring/prometheus/values.yaml`** — example:

```yaml
prometheus:
  prometheusSpec:
    retention: 15d
    storageSpec:
      volumeClaimTemplate:
        spec:
          resources:
            requests:
              storage: 10Gi

grafana:
  adminPassword: "your-secure-password"
  persistence:
    enabled: true
    size: 5Gi
  dashboardProviders:
    dashboardproviders.yaml:
      apiVersion: 1
      providers:
        - name: 'default'
          orgId: 1
          folder: ''
          type: file
          disableDeletion: false
          options:
            path: /var/lib/grafana/dashboards/default
```

### Access Grafana

```bash
kubectl port-forward svc/kube-prometheus-stack-grafana -n monitoring 3000:80
```

Open: **http://localhost:3000**
- Username: `admin`
- Password: *(value set in values.yaml)*

### Access Prometheus

```bash
kubectl port-forward svc/kube-prometheus-stack-prometheus -n monitoring 9090:9090
```

Open: **http://localhost:9090**

### Verify Metrics Are Being Scraped

```bash
kubectl get servicemonitors -n monitoring
kubectl get prometheusrules -n monitoring
```

### Import Dashboards

Grafana comes pre-loaded with Kubernetes dashboards. You can also import these popular dashboard IDs:

| Dashboard | ID |
|-----------|-----|
| Kubernetes Cluster Overview | `7249` |
| Node Exporter Full | `1860` |
| Kubernetes Pods | `6417` |
| ArgoCD | `14584` |

Go to **Grafana → Dashboards → Import** and enter the ID.

### Custom Alert Rules

```yaml
# monitoring/prometheus/alerting-rules.yaml
apiVersion: monitoring.coreos.com/v1
kind: PrometheusRule
metadata:
  name: k8s-project-alerts
  namespace: monitoring
spec:
  groups:
    - name: pod-alerts
      rules:
        - alert: PodCrashLooping
          expr: rate(kube_pod_container_status_restarts_total[5m]) > 0
          for: 5m
          labels:
            severity: critical
          annotations:
            summary: "Pod {{ $labels.pod }} is crash looping"

        - alert: HighCPUUsage
          expr: container_cpu_usage_seconds_total > 0.8
          for: 2m
          labels:
            severity: warning
          annotations:
            summary: "High CPU on pod {{ $labels.pod }}"
```

Apply with:
```bash
kubectl apply -f monitoring/prometheus/alerting-rules.yaml
```

---

## 📄 Kubernetes Manifests

### Deployment (`k8s/deployment.yaml`)

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: k8s-project
  namespace: default
  labels:
    app: k8s-project
spec:
  replicas: 2
  selector:
    matchLabels:
      app: k8s-project
  template:
    metadata:
      labels:
        app: k8s-project
    spec:
      containers:
        - name: app
          image: devops0014/k8s-project:latest
          ports:
            - containerPort: 80
          resources:
            requests:
              memory: "64Mi"
              cpu: "250m"
            limits:
              memory: "128Mi"
              cpu: "500m"
          readinessProbe:
            httpGet:
              path: /health
              port: 80
            initialDelaySeconds: 5
            periodSeconds: 10
          livenessProbe:
            httpGet:
              path: /health
              port: 80
            initialDelaySeconds: 15
            periodSeconds: 20
```

### Service (`k8s/service.yaml`)

```yaml
apiVersion: v1
kind: Service
metadata:
  name: k8s-project-svc
  namespace: default
spec:
  selector:
    app: k8s-project
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: LoadBalancer
```

---

## ⚙️ CI/CD Pipeline

This project uses **Jenkins** as the CI engine and **ArgoCD** as the CD engine (GitOps model).

```
Developer pushes code
       │
       ▼
Jenkins detects webhook → runs Jenkinsfile pipeline
       │
       ├── Stage 1: Checkout
       ├── Stage 2: Build Docker image
       ├── Stage 3: Push image to DockerHub
       └── Stage 4: Update image tag in k8s/deployment.yaml → Git push
                         │
                         ▼
              ArgoCD detects Git change → auto-deploys to cluster ✅
```

### Jenkins Prerequisites

Install these Jenkins plugins:
- **Git Plugin** — to clone the repo
- **Docker Pipeline Plugin** — to build & push Docker images
- **Pipeline Plugin** — for declarative pipelines
- **Credentials Binding Plugin** — for secrets management

### Add Credentials in Jenkins

Go to **Jenkins → Manage Jenkins → Credentials → Global → Add Credentials**:

| ID | Type | Description |
|----|------|-------------|
| `dockerhub-credentials` | Username/Password | DockerHub login |
| `github-credentials` | Username/Password or SSH | GitHub access for git push |

### Jenkinsfile

Place this `Jenkinsfile` at the root of your repository:

```groovy
pipeline {
    agent any

    environment {
        IMAGE_NAME     = "devops0014/k8s-project"
        IMAGE_TAG      = "${BUILD_NUMBER}"
        REGISTRY_CREDS = credentials('dockerhub-credentials')
        GIT_REPO       = "https://github.com/devops0014/k8s-project.git"
        GIT_BRANCH     = "main"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: "${GIT_BRANCH}",
                    credentialsId: 'github-credentials',
                    url: "${GIT_REPO}"
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${IMAGE_NAME}:${IMAGE_TAG}", "./app")
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-credentials') {
                        dockerImage.push("${IMAGE_TAG}")
                        dockerImage.push("latest")
                    }
                }
            }
        }

        stage('Update K8s Manifest') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'github-credentials',
                    usernameVariable: 'GIT_USER',
                    passwordVariable: 'GIT_PASS'
                )]) {
                    sh """
                        git config user.email "jenkins@ci.local"
                        git config user.name "Jenkins"

                        # Update image tag in deployment manifest
                        sed -i 's|${IMAGE_NAME}:.*|${IMAGE_NAME}:${IMAGE_TAG}|' k8s/deployment.yaml

                        git add k8s/deployment.yaml
                        git commit -m "ci: update image tag to ${IMAGE_TAG} [skip ci]"
                        git push https://${GIT_USER}:${GIT_PASS}@github.com/devops0014/k8s-project.git ${GIT_BRANCH}
                    """
                }
            }
        }
    }

    post {
        success {
            echo "✅ Build ${IMAGE_TAG} pushed. ArgoCD will deploy automatically."
        }
        failure {
            echo "❌ Pipeline failed. Check logs above."
        }
        always {
            // Clean up local Docker image to save disk space
            sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG} || true"
        }
    }
}
```

### Configure Webhook (GitHub → Jenkins)

1. In Jenkins, go to your pipeline job → **Configure** → enable **GitHub hook trigger for GITScm polling**
2. In GitHub repo → **Settings → Webhooks → Add webhook**
   - Payload URL: `http://<JENKINS_IP>:8080/github-webhook/`
   - Content type: `application/json`
   - Trigger: **Just the push event**

> ArgoCD detects the manifest update commit and auto-deploys the new image to the cluster. 🎯

---

## 🌍 Accessing Services

| Service | Method | URL |
|---------|--------|-----|
| Application | `kubectl port-forward svc/k8s-project-svc 8000:80` | http://localhost:8000 |
| ArgoCD UI | `kubectl port-forward svc/argocd-server -n argocd 8080:443` | https://localhost:8080 |
| Grafana | `kubectl port-forward svc/kube-prometheus-stack-grafana -n monitoring 3000:80` | http://localhost:3000 |
| Prometheus | `kubectl port-forward svc/kube-prometheus-stack-prometheus -n monitoring 9090:9090` | http://localhost:9090 |

---

## 🔧 Troubleshooting

**Pods not starting?**
```bash
kubectl describe pod <pod-name>
kubectl logs <pod-name> --previous
```

**Jenkins pipeline failing on Docker push?**
```bash
# Ensure Jenkins user is in the docker group on the Jenkins host
sudo usermod -aG docker jenkins
sudo systemctl restart jenkins
```

**ArgoCD out of sync?**
```bash
argocd app sync k8s-project --force
```

**Prometheus not scraping targets?**
```bash
kubectl get servicemonitor -n monitoring
# Check Prometheus UI → Status → Targets
```

**Grafana shows no data?**
- Verify Prometheus data source is configured: **Grafana → Configuration → Data Sources**
- Ensure the URL is: `http://kube-prometheus-stack-prometheus.monitoring.svc:9090`

---

## 🤝 Contributing

1. Fork this repository
2. Create a feature branch: `git checkout -b feature/my-feature`
3. Commit your changes: `git commit -m 'feat: add my feature'`
4. Push to the branch: `git push origin feature/my-feature`
5. Open a Pull Request

---

