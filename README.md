# 🎮 Super Mario Game Deployment on AWS EKS using Terraform

This project demonstrates **end-to-end cloud deployment** of a Super Mario game using:

* AWS EC2
* Terraform Infrastructure as Code
* Amazon EKS (Elastic Kubernetes Service)
* Docker
* Kubernetes
* AWS Load Balancer

The project automates infrastructure creation and deploys the application on Kubernetes running inside AWS EKS.

---

## 🚀 Architecture Overview

```
EC2 → Terraform → AWS Infrastructure
              ↓
            EKS Cluster
              ↓
        Kubernetes Deployment
              ↓
        AWS Load Balancer
              ↓
            Browser
```

### Infrastructure Components Created

* VPC
* EKS Cluster
* Worker Node Group
* Kubernetes Deployment
* Load Balancer Service

---

## 🛠️ Prerequisites

* AWS Account
* Basic knowledge of AWS, Docker, Kubernetes
* GitHub access
* Minimum EC2 instance with 4GB RAM

⚠️ **Do NOT use:** `t3.micro` or `t3.small`

Recommended:

* `t3.medium`
* `c7i-flex.large`

---

# ⚡ Deployment Steps

---

## ✅ STEP 1 — Launch EC2 Instance

Go to:

```
AWS Console → EC2 → Instances → Launch Instance
```

### Settings

* AMI → Ubuntu
* Instance Type → `t3.medium` / `c7i-flex.large`
* Key Pair → Create new
* Security Group → Allow **All Traffic**

Launch instance.

---

## ✅ STEP 2 — Connect to EC2

```
EC2 → Select Instance → Connect → EC2 Instance Connect
```

You will get a terminal.

---

## ✅ STEP 3 — Update System

```bash
sudo su
apt update -y
```

---

## ✅ STEP 4 — Install Docker

```bash
apt install docker.io -y
usermod -aG docker ubuntu
newgrp docker
docker --version
```

---

## ✅ STEP 5 — Install Terraform

```bash
wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg

echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | tee /etc/apt/sources.list.d/hashicorp.list

apt update && apt install terraform -y
terraform -version
```

---

## ✅ STEP 6 — Install AWS CLI

```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
apt install unzip -y
unzip awscliv2.zip
./aws/install
aws --version
```

---

## ✅ STEP 7 — Install kubectl

```bash
apt install curl -y

curl -LO https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl

install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

kubectl version --client
```

---

## ✅ STEP 8 — Create IAM Role for EC2

Go to:

```
AWS Console → IAM → Roles → Create Role
```

* Trusted Entity → EC2
* Permission → `AdministratorAccess`
* Role Name → `ec2-admin-role`

---

## ✅ STEP 9 — Attach IAM Role to EC2

```
EC2 → Instance → Actions → Security → Modify IAM Role
```

Attach:

```
ec2-admin-role
```

---

## ✅ STEP 10 — Clone Project

```bash
mkdir supermario
cd supermario

git clone https://github.com/akshu20791/supermario-game
cd supermario-game/EKS-TF
```

---

## ✅ STEP 11 — Create S3 Bucket (Terraform Backend)

Go to AWS:

```
S3 → Create Bucket
```

Example:

```
suman-terraform-state
```

Note:

* Bucket name
* Region

---

## ✅ STEP 12 — Update Terraform Backend

```bash
vim backend.tf
```

Replace:

```hcl
bucket = "YOUR_BUCKET_NAME"
region = "YOUR_REGION"
```

Save:

```
ESC → :wq
```

---

## ✅ STEP 13 — Create Infrastructure

```bash
terraform init
terraform validate
terraform plan
terraform apply --auto-approve
```

⏳ Wait 10–15 minutes.

This creates:

* VPC
* EKS Cluster
* Node Groups
* Load Balancer

---

# ⚠️ Free Tier Fix (Important)

If using `c7i-flex.large`, update Terraform configuration.

### Update Node Group

```bash
cd ~/supermario/supermario-game/EKS-TF
vim main.tf
```

Search:

```
/aws_eks_node_group
```

Replace with:

```hcl
instance_types = ["c7i-flex.large"]

scaling_config {
 desired_size = 1
 max_size     = 2
 min_size     = 1
}
```

---

### Clean Failed Resources

```bash
terraform destroy --auto-approve
terraform refresh
terraform destroy --auto-approve
terraform init
terraform apply --auto-approve
```

---

## ✅ STEP 14 — Configure Kubernetes

```bash
aws eks update-kubeconfig --name EKS_CLOUD --region us-east-1
kubectl get nodes
```

Expected:

```
STATUS = Ready
```

---

## ✅ STEP 15 — Deploy Application

```bash
cd ..
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
kubectl get all
```

---

## ✅ STEP 16 — Get Load Balancer URL

```bash
kubectl describe service mario-service
```

Find:

```
LoadBalancer Ingress: xxxxxx.amazonaws.com
```

---

## ✅ STEP 17 — Access Application

Open in browser:

```
http://<load-balancer-url>
```

⚠️ Use **HTTP only** (not HTTPS).

You will see the Super Mario game.

---

## 💰 STEP 18 — Delete Infrastructure (Avoid Charges)

```bash
cd EKS-TF
terraform destroy --auto-approve
```

---

# 📦 Tech Stack

* AWS EC2
* AWS EKS
* Terraform
* Docker
* Kubernetes
* AWS CLI
* kubectl

---

# 🎯 Learning Outcomes

* Infrastructure as Code using Terraform
* Kubernetes deployment on AWS EKS
* Cloud-native application deployment
* AWS Load Balancer integration
* DevOps workflow implementation

---

# 👨‍💻 Author

**Suman M**
AI Graduate
