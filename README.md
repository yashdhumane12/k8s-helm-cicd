# 🚀 Kubernetes Deployment with Helm + GitHub Actions

A production-ready DevOps project demonstrating end-to-end CI/CD pipeline for a containerized Python Flask application deployed on Kubernetes using Helm charts.

## 🏗️ Architecture

```
GitHub Push → GitHub Actions CI/CD
                  ├── Build & Test (Python + pytest)
                  ├── Docker Build & Push → Docker Hub
                  ├── Helm Lint & Validate
                  └── Helm Deploy → Kubernetes Cluster
```

## 📁 Project Structure

```
k8s-helm-cicd/
├── app/
│   ├── app.py              # Flask application
│   ├── requirements.txt    # Python dependencies
│   └── Dockerfile          # Multi-stage Docker build
├── helm/
│   └── myapp/
│       ├── Chart.yaml      # Helm chart metadata
│       ├── values.yaml     # Default configuration values
│       └── templates/
│           ├── _helpers.tpl        # Helm template helpers
│           ├── deployment.yaml     # Kubernetes Deployment
│           ├── service.yaml        # Kubernetes Service
│           ├── ingress.yaml        # Kubernetes Ingress
│           ├── hpa.yaml            # Horizontal Pod Autoscaler
│           └── serviceaccount.yaml # Service Account
├── .github/
│   └── workflows/
│       └── ci-cd.yml       # GitHub Actions pipeline
└── README.md
```

## 🛠️ Tech Stack

| Tool | Purpose |
|------|---------|
| Python / Flask | Application |
| Docker | Containerization (multi-stage build) |
| Helm 3 | Kubernetes package manager |
| Kubernetes | Container orchestration |
| GitHub Actions | CI/CD pipeline |
| HPA | Auto-scaling |

## ⚙️ GitHub Actions Pipeline

The pipeline has **3 jobs**:

1. **Build & Test** — runs pytest, builds Docker image, pushes to Docker Hub
2. **Helm Validate** — lints Helm chart, runs template dry-run
3. **Deploy** — runs `helm upgrade --install` on the cluster (only on `main` push)

## 🔐 Required GitHub Secrets

Go to `Settings → Secrets and variables → Actions` and add:

| Secret | Description |
|--------|-------------|
| `DOCKERHUB_USERNAME` | Your Docker Hub username |
| `DOCKERHUB_TOKEN` | Docker Hub access token |
| `KUBECONFIG` | Base64-encoded kubeconfig file |

Generate kubeconfig secret:
```bash
cat ~/.kube/config | base64 -w 0
```

## 🚀 Getting Started

### Prerequisites
- Docker
- kubectl
- Helm 3
- A Kubernetes cluster (Minikube / Kind / EKS / GKE / AKS)

### Local Run

```bash
# Build and run locally
cd app
docker build -t myapp:local .
docker run -p 5000:5000 myapp:local

# Test endpoints
curl http://localhost:5000/
curl http://localhost:5000/health
curl http://localhost:5000/ready
```

### Manual Helm Deploy

```bash
# Update image repository in values.yaml first
helm upgrade --install myapp helm/myapp/ \
  --set image.repository=your-dockerhub-username/myapp \
  --set image.tag=latest \
  --namespace default \
  --create-namespace

# Check status
kubectl get pods
kubectl get svc
```

### Update values for your environment

Edit `helm/myapp/values.yaml`:
```yaml
image:
  repository: YOUR_DOCKERHUB_USERNAME/myapp  # ← change this
```

## 📊 Features

- ✅ Multi-stage Docker build (smaller, secure images)
- ✅ Non-root container user
- ✅ Liveness & Readiness probes
- ✅ Horizontal Pod Autoscaler (scales 2–5 pods on CPU)
- ✅ Rolling update strategy (zero downtime)
- ✅ Resource limits & requests
- ✅ GitHub Actions with Docker layer caching
- ✅ Helm chart with templating best practices

## 📜 License

MIT
