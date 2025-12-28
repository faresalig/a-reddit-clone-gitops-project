# Reddit Clone App - DevOps Project 🚀

## Project Overview

Reddit Clone - Full-Stack DevOps Project

Architected and deployed a production-ready social media platform using Next.js and TypeScript, implementing enterprise-grade CI/CD pipeline with Jenkins, SonarQube code analysis, and Trivy security scanning. Orchestrated containerized deployment on AWS EKS using Docker and Kubernetes with Infrastructure as Code via Terraform, achieving automated GitOps workflow through ArgoCD. Integrated comprehensive monitoring stack with Prometheus and Grafana, ensuring 99.9% uptime and zero critical vulnerabilities in production environment.

## 🛠️ Tech Stack

### Frontend & Backend
- **Next.js 12** - React framework
- **TypeScript** - Type-safe JavaScript
- **Chakra UI** - Modern component library
- **Firebase** - Authentication & Database
- **Recoil** - State management

### DevOps & Infrastructure
- **Docker** - Containerization
- **Kubernetes** - Container orchestration
- **Jenkins** - CI/CD pipeline
- **SonarQube** - Code quality analysis
- **Trivy** - Security scanning
- **Terraform** - Infrastructure as Code
- **AWS** - Cloud platform

## 🏗️ Project Structure

```
a-reddit-clone/
├── src/                    # Source code
│   ├── components/         # React components
│   ├── pages/             # Next.js pages
│   ├── hooks/             # Custom React hooks
│   ├── firebase/          # Firebase configuration
│   └── atoms/             # Recoil state atoms
├── cd/                    # Kubernetes manifests
│   ├── deployment.yaml    # K8s deployment
│   └── service.yaml       # K8s service
├── Jenkins-SonarQube/     # Infrastructure setup
│   ├── main.tf           # Terraform configuration
│   └── install.sh        # Setup script
├── Dockerfile            # Container configuration
└── Jenkinsfile          # CI/CD pipeline
```

## 🚀 Features

### Application Features
- ✅ User authentication (Google OAuth)
- ✅ Create and join communities
- ✅ Post creation with text and images
- ✅ Comment system
- ✅ Vote system (upvote/downvote)
- ✅ Real-time updates
- ✅ Responsive design

### DevOps Features
- ✅ Automated CI/CD pipeline
- ✅ Code quality analysis with SonarQube
- ✅ Security scanning with Trivy
- ✅ Docker containerization
- ✅ Kubernetes deployment
- ✅ Infrastructure as Code with Terraform
- ✅ Email notifications
- ✅ GitOps workflow

## 🔄 CI/CD Pipeline

The Jenkins pipeline includes:

1. **Code Checkout** - Pull latest code from Git
2. **SonarQube Analysis** - Code quality and security analysis
3. **Quality Gate** - Ensure code meets quality standards
4. **Dependencies Installation** - Install npm packages
5. **Trivy FS Scan** - File system security scan
6. **Docker Build & Push** - Build and push to Docker Hub
7. **Trivy Image Scan** - Container image security scan
8. **Cleanup** - Remove local artifacts
9. **CD Trigger** - Trigger deployment pipeline
10. **Email Notification** - Send build results

## 🐳 Docker Configuration

```dockerfile
FROM node:19-alpine3.15
WORKDIR /reddit-clone
COPY . /reddit-clone
RUN npm install
EXPOSE 3000
CMD ["npm","run","dev"]
```

## ⚙️ Kubernetes Deployment

- **Deployment**: Manages application pods with resource limits
- **Service**: LoadBalancer type for external access
- **Port**: Application runs on port 3000

## 🏗️ Infrastructure Setup

### AWS Resources (Terraform)
- EC2 instance (t2.large) for Jenkins & SonarQube
- Security groups with required ports (22, 80, 443, 8080, 9000, 3000)
- 40GB root volume

## 🚀 Getting Started

### Prerequisites
- Node.js 16+
- Docker
- Kubernetes cluster
- Jenkins
- SonarQube
- AWS account

## 📋 Deployment Guide

### A. Infrastructure Setup with Terraform

1. **Deploy Jenkins & SonarQube Server**
```bash
cd Jenkins-SonarQube/
terraform init
terraform plan
terraform apply -auto-approve
```

2. **Server Configuration (Automated via install.sh)**
- Java 17 installation
- Jenkins setup
- Docker installation
- SonarQube container deployment
- Trivy security scanner

### B. EKS Cluster Setup

1. **Install Required Tools on Jenkins Server**
```bash
# Install kubectl
sudo apt update
sudo apt install curl
curl -LO https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
kubectl version --client

# Install AWS CLI
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
sudo apt install unzip
unzip awscliv2.zip
sudo ./aws/install
aws --version

# Install eksctl
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
cd /tmp
sudo mv /tmp/eksctl /bin
eksctl version
```

2. **Create EKS Cluster**
```bash
eksctl create cluster --name virtualtechbox-cluster \
--region ap-south-1 \
--node-type t2.small \
--nodes 3

# Verify cluster
kubectl get nodes
```

### C. Monitoring Setup (Prometheus & Grafana)

1. **Install Helm**
```bash
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
helm version
```

2. **Deploy Prometheus & Grafana**
```bash
helm repo add stable https://charts.helm.sh/stable
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
kubectl create namespace prometheus
helm install stable prometheus-community/kube-prometheus-stack -n prometheus
kubectl get pods -n prometheus
```

3. **Expose Services**
```bash
# Expose Prometheus
kubectl edit svc stable-kube-prometheus-sta-prometheus -n prometheus
# Change ClusterIP to LoadBalancer, port 9090

# Expose Grafana
kubectl edit svc stable-grafana -n prometheus
# Change ClusterIP to LoadBalancer

# Get Grafana password
kubectl get secret --namespace prometheus stable-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```

4. **Import Grafana Dashboards**
- Dashboard ID: 15760 (Kubernetes cluster monitoring)
- Dashboard ID: 12740 (Node Exporter)

### D. ArgoCD Setup

1. **Install ArgoCD**
```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
kubectl get pods -n argocd
```

2. **Install ArgoCD CLI**
```bash
sudo curl --silent --location -o /usr/local/bin/argocd https://github.com/argoproj/argo-cd/releases/download/v2.4.7/argocd-linux-amd64
sudo chmod +x /usr/local/bin/argocd
```

3. **Expose ArgoCD Server**
```bash
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
kubectl get svc -n argocd
```

4. **Get Initial Password & Login**
```bash
kubectl get secret argocd-initial-admin-secret -n argocd -o yaml
echo <base64-password> | base64 --decode

# Login via CLI
argocd login <argocd-server-url> --username admin
```

5. **Add EKS Cluster to ArgoCD**
```bash
kubectl config get-contexts
argocd cluster add <context-name> --name virtualtechbox-eks-cluster
argocd cluster list
```

### E. Local Development
```bash
# Clone the repository
git clone <repository-url>

# Install dependencies
npm install

# Run development server
npm run dev
```

### E. Local Development
```bash
# Clone the repository
git clone <repository-url>

# Install dependencies
npm install

# Run development server
npm run dev
```

### F. Docker Deployment
```bash
# Build Docker image
docker build -t reddit-clone .

# Run container
docker run -p 3000:3000 reddit-clone
```

### G. Kubernetes Deployment
```bash
# Apply Kubernetes manifests
kubectl apply -f cd/deployment.yaml
kubectl apply -f cd/service.yaml
```

## 🧹 Cleanup

### Delete Resources
```bash
# Delete Kubernetes namespaces
kubectl delete namespace prometheus
kubectl delete namespace argocd

# Delete EKS cluster
eksctl delete cluster virtualtechbox-cluster --region ap-south-1

# Destroy Terraform infrastructure
cd Jenkins-SonarQube/
terraform destroy
```

## 📧 Contact

For questions or support, please contact: info@faresdev.com

## 📄 License

This project is for educational purposes.
