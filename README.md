# 🎮 2048 Game DevOps Pipeline

Complete DevOps implementation of the 2048 game with Kubernetes, CI/CD, and GitOps workflows. Demonstrates modern DevOps practices using AWS EKS and local Minikube environments.

## 🚀 Quick Start

```bash
# Clone and setup
git clone https://github.com/sudo-hrmn/2048-game.git
cd 2048-game

# Start local Kubernetes
minikube start --driver=docker --cpus=2 --memory=4096
minikube addons enable ingress

# Deploy services
kubectl apply -f jenkins-deployment.yaml
kubectl apply -f 2048-full-deployment.yaml

# Setup ArgoCD
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Access game
echo "$(minikube ip) 2048.local" | sudo tee -a /etc/hosts
```

## 🛠️ Technologies Stack

| Technology | Purpose | Status |
|------------|---------|--------|
| **Kubernetes** | Container Orchestration | ✅ |
| **Minikube** | Local K8s Development | ✅ |
| **AWS EKS** | Production K8s Cluster | ✅ |
| **Jenkins** | CI/CD Pipeline | ✅ |
| **ArgoCD** | GitOps Deployment | ✅ |
| **NGINX Ingress** | Load Balancing | ✅ |
| **Docker** | Containerization | ✅ |

## 📦 Local Deployment

### Prerequisites
- Docker
- Minikube
- kubectl

### Setup Steps

1. **Start Minikube**
```bash
minikube start --driver=docker --cpus=2 --memory=4096
minikube addons enable ingress
```

2. **Deploy Jenkins**
```bash
kubectl apply -f jenkins-deployment.yaml
```

3. **Deploy 2048 Game**
```bash
kubectl apply -f 2048-full-deployment.yaml
echo "$(minikube ip) 2048.local" | sudo tee -a /etc/hosts
```

4. **Deploy ArgoCD**
```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

## ☁️ AWS EKS Deployment

### Prerequisites
- AWS CLI configured
- eksctl installed

### EKS Setup

1. **Create Cluster**
```bash
eksctl create cluster --name demo-cluster --region us-east-1 --fargate
```

2. **Configure kubectl**
```bash
aws eks update-kubeconfig --name demo-cluster --region us-east-1
```

3. **Deploy Application**
```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/examples/2048/2048_full.yaml
```

## 🔧 CI/CD Pipeline

### Jenkins Access
- **URL**: `http://<minikube-ip>:32722`
- **Username**: `admin`
- **Password**: `kubectl exec -n jenkins deployment/jenkins -- cat /var/jenkins_home/secrets/initialAdminPassword`

## 🎯 GitOps with ArgoCD

### ArgoCD Access
- **URL**: `https://localhost:8081` (port-forward: `kubectl port-forward svc/argocd-server -n argocd 8081:443`)
- **Username**: `admin`
- **Password**: `kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d`

## 🌐 Access Applications

### Local Environment
| Service | URL |
|---------|-----|
| **2048 Game** | http://2048.local |
| **Jenkins** | http://192.168.49.2:32722 |
| **ArgoCD** | https://localhost:8081 |
| **K8s Dashboard** | `minikube dashboard` |

## 🧹 Cleanup

### Local Environment
```bash
# Delete deployments
kubectl delete -f 2048-full-deployment.yaml
kubectl delete -f jenkins-deployment.yaml
kubectl delete namespace argocd

# Stop minikube
minikube stop
minikube delete
```

### AWS EKS Environment
```bash
eksctl delete cluster --name demo-cluster --region us-east-1
```

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Commit your changes
4. Push to the branch
5. Open a Pull Request

---

**Made with ❤️ for the DevOps community**
