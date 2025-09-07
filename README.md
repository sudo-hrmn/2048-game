# 🎮 2048 Game DevOps Pipeline

A complete DevOps implementation of the classic 2048 game with Kubernetes, CI/CD, and GitOps workflows. This project demonstrates modern DevOps practices using both AWS EKS and local Minikube environments.

![2048 Game](screenshots/2048-game-running.md)
![DevOps Dashboard](screenshots/devops-dashboard.md)

## 🚀 Quick Start

```bash
# Clone the repository
git clone https://github.com/sudo-hrmn/2048-game.git
cd 2048-game

# Start local deployment
minikube start --driver=docker --cpus=2 --memory=4096
minikube addons enable ingress dashboard metrics-server

# Deploy all services
kubectl apply -f jenkins-deployment.yaml
kubectl apply -f 2048-full-deployment.yaml
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Access applications
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

## 📋 Table of Contents

## Understanding Kubernetes Fundamentals

### 1.1 EKS vs. Self-Managed Kubernetes: Pros and Cons

1.1.1 EKS (Amazon Elastic Kubernetes Service)
Pros:

    Managed Control Plane: EKS takes care of managing the Kubernetes control plane components, such as the API server, controller manager, and etcd. AWS handles upgrades, patches, and ensures high availability of the control plane.

    Automated Updates: EKS automatically updates the Kubernetes version, eliminating the need for manual intervention and ensuring that the cluster stays up-to-date with the latest features and security patches.

    Scalability: EKS can automatically scale the Kubernetes control plane based on demand, ensuring the cluster remains responsive as the workload increases.

    AWS Integration: EKS seamlessly integrates with various AWS services, such as AWS IAM for authentication and authorization, Amazon VPC for networking, and AWS Load Balancers for service exposure.

    Security and Compliance: EKS is designed to meet various security standards and compliance requirements, providing a secure and compliant environment for running containerized workloads.

    Monitoring and Logging: EKS integrates with AWS CloudWatch for monitoring cluster health and performance metrics, making it easier to track and troubleshoot issues.

    Ecosystem and Community: Being a managed service, EKS benefits from continuous improvement, support, and contributions from the broader Kubernetes community.

Cons:

    Cost: EKS is a managed service, and this convenience comes at a cost. Running an EKS cluster may be more expensive compared to self-managed Kubernetes, especially for large-scale deployments.

    Less Control: While EKS provides a great deal of automation, it also means that you have less control over the underlying infrastructure and some Kubernetes configurations.

1.1.2 Self-Managed Kubernetes on EC2 Instances
Pros:

    Cost-Effective: Self-managed Kubernetes allows you to take advantage of EC2 spot instances and reserved instances, potentially reducing the overall cost of running Kubernetes clusters.

    Flexibility: With self-managed Kubernetes, you have full control over the cluster's configuration and infrastructure, enabling customization and optimization for specific use cases.

    EKS-Compatible: Self-managed Kubernetes on AWS can still leverage various AWS services and features, enabling integration with existing AWS resources.

    Experimental Features: Self-managed Kubernetes allows you to experiment with the latest Kubernetes features and versions before they are officially supported by EKS.

Cons:

    Complexity: Setting up and managing a self-managed Kubernetes cluster can be complex and time-consuming, especially for those new to Kubernetes or AWS.

    Maintenance Overhead: Self-managed clusters require manual management of Kubernetes control plane updates, patches, and high availability.

    Scaling Challenges: Scaling the control plane of a self-managed cluster can be challenging, and it requires careful planning to ensure high availability during scaling events.

    Security and Compliance: Self-managed clusters may require additional effort to implement best practices for security and compliance compared to EKS, which comes with some built-in security features.

    Lack of Automation: Self-managed Kubernetes requires more manual intervention and scripting for certain operations, which can increase the risk of human error.

## Setting up your AWS Environment for EKS

Sure! Let's go into detail for each subsection:

## 2.1 Creating an AWS Account and Setting up IAM Users

Creating an AWS account is the first step to access and utilize AWS services, including Amazon Elastic Kubernetes Service (EKS). Here's a step-by-step guide to creating an AWS account and setting up IAM users:

1. **Create an AWS Account**:
   - Go to the AWS website (https://aws.amazon.com/) and click on the "Create an AWS Account" button.
   - Follow the on-screen instructions to provide your email address, password, and required account details.
   - Enter your payment information to verify your identity and set up billing.

2. **Access AWS Management Console**:
   - After creating the account, you will receive a verification email. Follow the link in the email to verify your account.
   - Log in to the AWS Management Console using your email address and password.

3. **Set up Multi-Factor Authentication (MFA)** (Optional but recommended):
   - Once you are logged in, set up MFA to add an extra layer of security to your AWS account. You can use MFA with a virtual MFA device or a hardware MFA device.

4. **Create IAM Users**:
   - Go to the IAM (Identity and Access Management) service in the AWS Management Console.
   - Click on "Users" in the left-hand navigation pane and then click on "Add user."
   - Enter a username for the new IAM user and select the access type (Programmatic access, AWS Management Console access, or both).
   - Choose the permissions for the IAM user by adding them to one or more IAM groups or attaching policies directly.
   - Optionally, set permissions boundary, tags, and enable MFA for the IAM user.

5. **Access Keys (for Programmatic Access)**:
   - If you selected "Programmatic access" during user creation, you will receive access keys (Access Key ID and Secret Access Key).
   - Store these access keys securely, as they will be used to authenticate API requests made to AWS services.

## 2.2 Configuring the AWS CLI and kubectl

With IAM users set up, you can now configure the AWS CLI and kubectl on your local machine to interact with AWS services and EKS clusters:

1. **Installing the AWS CLI**:
   - Download and install the AWS CLI on your local machine. You can find installation instructions for various operating systems [here](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html).

2. **Configuring AWS CLI Credentials**:
   - Open a terminal or command prompt and run the following command:
     ```
     aws configure
     ```
   - Enter the access key ID and secret access key of the IAM user you created earlier.
   - Choose a default region and output format for AWS CLI commands.

3. **Installing kubectl**:
   - Install kubectl on your local machine. Instructions can be found [here](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

4. **Configuring kubectl for EKS**:
   - Once kubectl is installed, you need to configure it to work with your EKS cluster.
   - In the AWS Management Console, go to the EKS service and select your cluster.
   - Click on the "Config" button and follow the instructions to update your kubeconfig file. Alternatively, you can use the AWS CLI to update the kubeconfig file:
     ```
     aws eks update-kubeconfig --name your-cluster-name
     ```
   - Verify the configuration by running a kubectl command against your EKS cluster:
     ```
     kubectl get nodes
     ```

## 2.3 Preparing Networking and Security Groups for EKS

Before launching an EKS cluster, you need to prepare the networking and security groups to ensure proper communication and security within the cluster:

1. **Creating an Amazon VPC (Virtual Private Cloud)**:
   - Go to the AWS Management Console and navigate to the VPC service.
   - Click on "Create VPC" and enter the necessary details like VPC name, IPv4 CIDR block, and subnets.
   - Create public and private subnets to distribute resources in different availability zones.

Sure! Let's go into detail for each of the points:

2. **Configuring Security Groups**

**Security Groups** are a fundamental aspect of Amazon Web Services (AWS) that act as virtual firewalls for your AWS resources, including Amazon Elastic Kubernetes Service (EKS) clusters. Security Groups control inbound and outbound traffic to and from these resources based on rules you define. Here's a step-by-step guide on configuring Security Groups for your EKS cluster:

1. **Create a Security Group**:
   - Go to the AWS Management Console and navigate to the Amazon VPC service.
   - Click on "Security Groups" in the left-hand navigation pane.
   - Click on "Create Security Group."
   - Provide a name and description for the Security Group.
   - Select the appropriate VPC for the Security Group.

2. **Inbound Rules**:
   - Define inbound rules to control incoming traffic to your EKS worker nodes.
   - By default, all inbound traffic is denied unless you explicitly allow it.
   - Common inbound rules include allowing SSH (port 22) access for administrative purposes and allowing ingress traffic from specific CIDR blocks or Security Groups.

3. **Outbound Rules**:
   - Define outbound rules to control the traffic leaving your EKS worker nodes.
   - By default, all outbound traffic is allowed unless you explicitly deny it.
   - For security purposes, you can restrict outbound traffic to specific destinations or ports.

4. **Security Group IDs**:
   - After creating the Security Group, you'll receive a Security Group ID. This ID will be used when launching your EKS worker nodes.

5. **Attach Security Group to EKS Worker Nodes**:
   - When launching the EKS worker nodes, specify the Security Group ID in the launch configuration. This associates the Security Group with the worker nodes, allowing them to communicate based on the defined rules.

Configuring Security Groups ensures that only the necessary traffic is allowed to and from your EKS worker nodes, enhancing the security of your EKS cluster.

3. **Setting Up Internet Gateway (IGW)**

An **Internet Gateway (IGW)** is a horizontally scaled, redundant, and highly available AWS resource that allows communication between your VPC and the internet. To enable EKS worker nodes to access the internet for tasks like pulling container images, you need to set up an Internet Gateway in your VPC. Here's how to do it:

1. **Create an Internet Gateway**:
   - Go to the AWS Management Console and navigate to the Amazon VPC service.
   - Click on "Internet Gateways" in the left-hand navigation pane.
   - Click on "Create Internet Gateway."
   - Provide a name for the Internet Gateway and click "Create Internet Gateway."

2. **Attach Internet Gateway to VPC**:
   - After creating the Internet Gateway, select the Internet Gateway in the list and click on "Attach to VPC."
   - Choose the VPC to which you want to attach the Internet Gateway and click "Attach."

3. **Update Route Tables**:
   - Go to "Route Tables" in the Amazon VPC service.
   - Identify the Route Table associated with the private subnets where your EKS worker nodes will be deployed.
   - Edit the Route Table and add a route with the destination `0.0.0.0/0` (all traffic) and the Internet Gateway ID as the target.

By setting up an Internet Gateway and updating the Route Tables, you provide internet access to your EKS worker nodes, enabling them to interact with external resources like container registries and external services.

4. **Configuring IAM Policies**

**Identity and Access Management (IAM)** is a service in AWS that allows you to manage access to AWS resources securely. IAM policies define permissions that specify what actions are allowed or denied on specific AWS resources. For your EKS cluster, you'll need to configure IAM policies to grant necessary permissions to your worker nodes and other resources. Here's how to do it:

1. **Create a Custom IAM Policy**:
   - Go to the AWS Management Console and navigate to the IAM service.
   - Click on "Policies" in the left-hand navigation pane.
   - Click on "Create policy."
   - Choose "JSON" as the policy language and define the permissions required for your EKS cluster. For example, you might need permissions for EC2 instances, Auto Scaling, Elastic Load Balancing, and accessing ECR (Elastic Container Registry).

2. **Attach the IAM Policy to IAM Roles**:
   - Go to "Roles" in the IAM service and select the IAM role that your EKS worker nodes will assume.
   - Click on "Attach policies" and search for the custom IAM policy you created in the previous step.
   - Attach the policy to the IAM role.

3. **Update EKS Worker Node Launch Configuration**:
   - When launching your EKS worker nodes, specify the IAM role ARN (Amazon Resource Name) of the IAM role that includes the necessary IAM policy.
   - The IAM role allows the worker nodes to authenticate with the EKS cluster and access AWS resources based on the permissions defined in the attached IAM policy.

By configuring IAM policies and associating them with IAM roles, you grant specific permissions to your EKS worker nodes, ensuring they can interact with AWS resources as needed while maintaining security and access control.

By completing these steps, your AWS environment is ready to host an Amazon EKS cluster. You can proceed with creating an EKS cluster using the AWS Management Console or AWS CLI as described in section 3.


- [🚀 Quick Start](#-quick-start)
- [🛠️ Technologies Stack](#️-technologies-stack)
- [📦 Local Deployment](#-local-deployment)
- [☁️ AWS EKS Deployment](#️-aws-eks-deployment)
- [🔧 CI/CD Pipeline](#-cicd-pipeline)
- [🎯 GitOps with ArgoCD](#-gitops-with-argocd)
- [🌐 Access Applications](#-access-applications)
- [📊 Monitoring & Observability](#-monitoring--observability)
- [🧹 Cleanup](#-cleanup)

## 📦 Local Deployment

### Prerequisites
- Docker
- Minikube
- kubectl
- Git

### Step-by-Step Local Setup

1. **Start Minikube Cluster**
```bash
minikube start --driver=docker --cpus=2 --memory=4096
minikube addons enable ingress dashboard metrics-server
```

2. **Deploy Jenkins CI/CD**
```bash
kubectl apply -f jenkins-deployment.yaml
```

3. **Deploy ArgoCD GitOps**
```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

4. **Deploy 2048 Game**
```bash
kubectl apply -f 2048-full-deployment.yaml
echo "$(minikube ip) 2048.local" | sudo tee -a /etc/hosts
```

## ☁️ AWS EKS Deployment

### Prerequisites
- AWS CLI configured
- eksctl installed
- kubectl installed

### EKS Cluster Setup

1. **Create EKS Cluster**
```bash
eksctl create cluster --name demo-cluster --region us-east-1 --fargate
```

2. **Configure kubectl**
```bash
aws eks update-kubeconfig --name demo-cluster --region us-east-1
```

3. **Deploy AWS Load Balancer Controller**
```bash
# Download IAM policy
curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.11.0/docs/install/iam_policy.json

# Create IAM Policy
aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam_policy.json

# Create IAM Role
eksctl create iamserviceaccount \
  --cluster=demo-cluster \
  --namespace=kube-system \
  --name=aws-load-balancer-controller \
  --role-name AmazonEKSLoadBalancerControllerRole \
  --attach-policy-arn=arn:aws:iam::<account-id>:policy/AWSLoadBalancerControllerIAMPolicy \
  --approve

# Install ALB Controller
helm repo add eks https://aws.github.io/eks-charts
helm repo update eks
helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
  -n kube-system \
  --set clusterName=demo-cluster \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller
```

4. **Deploy 2048 Application**
```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/examples/2048/2048_full.yaml
```

## 🔧 CI/CD Pipeline

### Jenkins Setup
- **URL**: `http://<minikube-ip>:32722`
- **Username**: `admin`
- **Password**: Get from `kubectl exec -n jenkins deployment/jenkins -- cat /var/jenkins_home/secrets/initialAdminPassword`

### Pipeline Features
- Automated builds on Git commits
- Docker image building and pushing
- Kubernetes deployment automation
- Integration with ArgoCD for GitOps

## 🎯 GitOps with ArgoCD

### ArgoCD Access
- **URL**: `https://localhost:8081` (port-forwarded)
- **Username**: `admin`
- **Password**: Get from `kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d`

### GitOps Workflow
1. Code changes pushed to Git repository
2. ArgoCD detects changes automatically
3. Applications deployed/updated in Kubernetes
4. Continuous synchronization with Git state

## 🌐 Access Applications

### Local Environment
| Service | URL | Credentials |
|---------|-----|-------------|
| **2048 Game** | http://2048.local | - |
| **Jenkins** | http://192.168.49.2:32722 | admin / [get-password] |
| **ArgoCD** | https://localhost:8081 | admin / [get-password] |
| **K8s Dashboard** | `minikube dashboard` | - |

### AWS EKS Environment
| Service | URL | Notes |
|---------|-----|-------|
| **2048 Game** | ALB DNS Name | Check ingress |
| **Jenkins** | LoadBalancer IP | If deployed |
| **ArgoCD** | LoadBalancer IP | If exposed |

## 📊 Monitoring & Observability

### Built-in Monitoring
- **Kubernetes Dashboard**: Cluster overview and resource monitoring
- **Metrics Server**: Resource usage metrics
- **ArgoCD Dashboard**: Application deployment status
- **Jenkins Dashboard**: Build and pipeline monitoring

### Health Checks
```bash
# Check all pods status
kubectl get pods --all-namespaces

# Check services
kubectl get svc --all-namespaces

# Check ingress
kubectl get ingress --all-namespaces

# Check ArgoCD applications
kubectl get applications -n argocd
```

## 🧹 Cleanup

### Local Environment
```bash
# Delete all deployments
kubectl delete -f 2048-full-deployment.yaml
kubectl delete -f jenkins-deployment.yaml
kubectl delete namespace argocd

# Stop and delete minikube
minikube stop
minikube delete

# Clean Docker resources
docker system prune -f
```

### AWS EKS Environment
```bash
# Delete the cluster
eksctl delete cluster --name demo-cluster --region us-east-1

# Clean up IAM resources
aws iam delete-policy --policy-arn arn:aws:iam::<account-id>:policy/AWSLoadBalancerControllerIAMPolicy
```

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- Kubernetes community for excellent documentation
- ArgoCD team for GitOps best practices
- Jenkins community for CI/CD pipeline examples
- AWS for EKS service and documentation

## 📞 Support

For support and questions:
- Create an issue in this repository
- Check the [documentation](docs/)
- Review existing issues and discussions

---

**Made with ❤️ for the DevOps community**
