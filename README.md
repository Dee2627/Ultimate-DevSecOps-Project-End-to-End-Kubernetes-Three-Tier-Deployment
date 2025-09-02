# Ultimate DevSecOps Project: End-to-End Kubernetes Three-Tier Deployment

A comprehensive DevSecOps implementation featuring automated CI/CD pipelines, security scanning, and monitoring on AWS EKS with GitOps deployment strategies.

## 🏗️ Architecture Overview

This project demonstrates a production-ready DevSecOps pipeline that integrates security at every stage of the software delivery lifecycle. The architecture includes:

- **Three-tier application** (Frontend, Backend, Database)
- **AWS EKS** for container orchestration
- **Automated CI/CD** with security gates
- **GitOps deployment** using ArgoCD
- **Comprehensive monitoring** with Prometheus and Grafana

## 🛠️ Technology Stack

### Infrastructure & Platform
- **AWS EKS** - Kubernetes cluster management
- **Terraform** - Infrastructure as Code
- **AWS EC2** - Jenkins and Jump server hosting
- **AWS ECR** - Container registry

### CI/CD & Automation
- **Jenkins** - Pipeline automation and orchestration
- **ArgoCD** - GitOps continuous deployment
- **Helm** - Kubernetes package management

### Security Tools
- **SonarQube** - Static code analysis and quality gates
- **OWASP Dependency-Check** - Vulnerability scanning for dependencies
- **Trivy** - Container image security scanning

### Monitoring & Observability
- **Prometheus** - Metrics collection and alerting
- **Grafana** - Visualization and dashboards

## 🚀 Key Features

- **Security-First Approach**: Integrated security scanning at build and deployment stages
- **Automated Infrastructure**: Terraform-based AWS resource provisioning
- **GitOps Workflow**: ArgoCD ensures deployment synchronization with Git repositories
- **Zero-Downtime Deployments**: Rolling updates with health checks
- **Comprehensive Monitoring**: Real-time cluster and application metrics
- **Quality Gates**: Automated code quality checks and security vulnerability assessments

## 📋 Prerequisites

- AWS Account with administrative access
- Basic understanding of Kubernetes, Docker, and CI/CD concepts
- Familiarity with Terraform and GitOps principles

## 🏃‍♂️ Quick Start

### 1. Infrastructure Setup

**Deploy Jenkins Server:**
```bash
# Launch EC2 instance with Ubuntu
# Instance type: t2.2xlarge (8 vCPUs, 32GB RAM)
# Security groups: 8080, 50000, 443, 80, 9000
# Attach IAM role with Administrator Access
```

**Automated Tool Installation (User Data Script):**
- Jenkins
- Docker
- Terraform
- AWS CLI
- SonarQube (containerized)
- Trivy
- kubectl
- eksctl
- Helm

### 2. EKS Cluster Deployment

Use the Jenkins pipeline to deploy EKS infrastructure:

1. Configure AWS credentials in Jenkins
2. Set up Terraform tools configuration
3. Run the infrastructure pipeline with parameters:
   - Environment: `dev`
   - Action: `plan` → `apply`

### 3. Jump Server Configuration

Deploy a bastion host for secure cluster access:
- Instance type: t2.medium
- Network: Public subnet in EKS VPC
- Tools: AWS CLI, kubectl, Helm, eksctl

### 4. Load Balancer Setup

Configure AWS Load Balancer Controller:
```bash
# Create IAM service account
eksctl create iamserviceaccount \
  --cluster=<cluster-name> \
  --namespace=kube-system \
  --name=aws-load-balancer-controller \
  --role-name AmazonEKSLoadBalancerRole \
  --attach-policy-arn=arn:aws:iam::<account-id>:policy/AWSLoadBalancerControllerIAMPolicy \
  --approve

# Install controller via Helm
helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
  --namespace kube-system \
  --set clusterName=<cluster-name> \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller
```

### 5. ArgoCD Installation

```bash
# Create namespace and install ArgoCD
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Expose ArgoCD server
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'

# Get admin password
kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 --decode
```

## 🔧 Pipeline Configuration

### Frontend Pipeline Features:
- Node.js build environment
- SonarQube code quality analysis
- OWASP dependency vulnerability scanning
- Trivy filesystem and image security scanning
- Docker image build and ECR push
- Automated Kubernetes manifest updates

### Backend Pipeline Features:
- Similar security and quality gates as frontend
- Separate ECR repository management
- Independent deployment lifecycle
- Coordinated with frontend through ArgoCD

## 📊 Monitoring Setup

### Prometheus & Grafana Installation:
```bash
# Add Helm repositories
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update

# Install monitoring stack
helm install prometheus prometheus-community/kube-prometheus-stack \
  --set prometheus.server.persistentVolume.storageClass=gp2 \
  --set alertmanager.alertmanagerSpec.persistentVolume.storageClass=gp2
```

### Pre-configured Dashboards:
- **Kubernetes Cluster Monitoring** (Dashboard ID: 6417)
- **Kubernetes Resource Monitoring** (Dashboard ID: 17375)

## 🔐 Security Implementation

### Static Analysis
- **SonarQube**: Code quality, security hotspots, and technical debt analysis
- **Quality Gates**: Pipeline fails if code doesn't meet defined quality standards

### Dependency Security
- **OWASP Dependency-Check**: Identifies known vulnerabilities in project dependencies
- **Automated vulnerability reporting**: XML reports generated for each scan

### Container Security
- **Trivy Scanning**: 
  - Filesystem scanning during build
  - Container image scanning before deployment
  - Critical and high severity vulnerability detection

### Infrastructure Security
- **IAM Roles**: Principle of least privilege access
- **VPC Isolation**: Private subnets for EKS cluster
- **Security Groups**: Restricted network access

## 🚦 Pipeline Workflow

### Build Stage
1. **Code Checkout**: Fetch latest code from Git repository
2. **Dependency Installation**: Install project dependencies
3. **Static Analysis**: SonarQube code quality and security analysis

### Security Stage
1. **Quality Gate**: Enforce code quality standards
2. **Dependency Check**: OWASP vulnerability scanning
3. **Filesystem Scan**: Trivy security analysis

### Package Stage
1. **Docker Build**: Create container images
2. **Image Scanning**: Trivy container vulnerability assessment
3. **Registry Push**: Upload to AWS ECR with build tags

### Deploy Stage
1. **Manifest Update**: Update Kubernetes deployment files with new image tags
2. **Git Commit**: Push updated manifests to repository
3. **ArgoCD Sync**: Automatic deployment to EKS cluster

## 📈 Monitoring & Observability

### Metrics Collection
- **Cluster Metrics**: Node performance, resource utilization
- **Application Metrics**: Pod health, deployment status
- **Infrastructure Metrics**: AWS resource consumption

### Alerting
- **Prometheus AlertManager**: Configurable alert rules
- **Grafana Notifications**: Dashboard-based alerting
- **Webhook Integration**: Custom notification channels

## 🔧 Configuration Details

### Jenkins Credentials Required:
- `aws-creds`: AWS Access Key and Secret
- `sonar-token`: SonarQube authentication token
- `Account_ID`: AWS Account ID
- `ECR_REPO1`: Frontend ECR repository name
- `ECR_REPO2`: Backend ECR repository name
- `GITHUB-APP`: GitHub username and personal access token
- `github`: GitHub personal access token

### AWS Permissions:
- EC2 management for Jenkins and Jump servers
- EKS cluster creation and management
- ECR repository access
- IAM role creation and assignment
- VPC and networking configuration

## 🧪 Testing the Implementation

### Verification Steps:

1. **Infrastructure Validation**:
   ```bash
   kubectl get nodes
   kubectl get namespaces
   ```

2. **Application Health Check**:
   ```bash
   kubectl get pods -n three-tier
   kubectl get svc -n three-tier
   ```

3. **ArgoCD Synchronization**:
   - Access ArgoCD UI
   - Verify all applications show "Synced" and "Healthy" status

4. **Monitoring Verification**:
   - Access Prometheus targets page
   - Check Grafana dashboard data population

## 🎯 Learning Outcomes

After completing this project, you will have hands-on experience with:

- **DevSecOps Integration**: Security embedded throughout the development lifecycle
- **Infrastructure as Code**: Terraform-based AWS resource management
- **Container Security**: Multi-layer security scanning and vulnerability management
- **GitOps Methodology**: Declarative, Git-driven deployment strategies
- **Kubernetes Operations**: Production-grade cluster management and monitoring
- **CI/CD Automation**: End-to-end pipeline construction and optimization

## 🤝 Contributing

This project serves as a learning resource and foundation for DevSecOps implementations. When contributing or adapting:

1. **Security First**: Maintain security scanning and quality gates
2. **Documentation**: Update this README for any architectural changes
3. **Best Practices**: Follow established DevOps and security guidelines
4. **Testing**: Verify all pipeline stages before committing changes

## ⚠️ Important Security Notes

- **Credential Management**: Never commit secrets to repositories
- **Network Security**: Restrict security group access to trusted sources
- **IAM Permissions**: Follow principle of least privilege
- **Container Security**: Regularly update base images and scan for vulnerabilities
- **Monitoring**: Set up appropriate alerting for security events

## 📚 Additional Resources

- [AWS EKS Best Practices](https://aws.github.io/aws-eks-best-practices/)
- [ArgoCD Documentation](https://argo-cd.readthedocs.io/)
- [SonarQube Quality Gates](https://docs.sonarqube.org/latest/user-guide/quality-gates/)
- [OWASP Dependency Check](https://owasp.org/www-project-dependency-check/)
- [Prometheus Monitoring](https://prometheus.io/docs/)

---

**Project Status**: ✅ Production Ready

This implementation demonstrates enterprise-grade DevSecOps practices suitable for production environments with appropriate security controls and monitoring capabilities.