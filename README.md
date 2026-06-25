# Trendify App – Application Deployment on AWS EKS with Jenkins CI/CD

## Overview

The primary objective of this project is to implement a complete end-to-end DevOps deployment pipeline for a React application by leveraging modern cloud-native technologies and automation tools. The project focuses on containerizing the application using Docker, provisioning AWS infrastructure through Terraform, deploying the application on Amazon EKS using Kubernetes, and automating the build, image publishing, and deployment processes through a Jenkins CI/CD pipeline integrated with GitHub.

## Architecture


```text
GitHub Repository
        │
        ▼
 Jenkins pipeline
        │
        ▼
 Docker Build
        │
        ▼
Docker Hub Registry
        │
        ▼
AWS EKS Cluster
        │
        ▼
LoadBalancer Service
        │
        ▼
 Application Access
```


### Components

| Component | Purpose |
|------------|----------|
| React Application | Frontend application |
| Docker | Containerization |
| DockerHub | Image Registry |
| Terraform | Infrastructure Provisioning |
| AWS EKS | Kubernetes Cluster |
| Jenkins | CI/CD Automation |
| GitHub | Source Code Management |

---

## Project Objectives

- Containerize the application using Docker.
- Push Docker images to DockerHub.
- Provision AWS infrastructure using Terraform.
- Create and manage an EKS cluster.
- Deploy the application using Kubernetes manifests.
- Configure Jenkins CI/CD pipeline.
- Automate build, push, and deployment process.

---

# Technology Stack

- ReactJS
- Docker
- DockerHub
- AWS
- Terraform
- Amazon EKS
- Kubernetes
- Jenkins
- GitHub

---

## Step 1: Application build Artifacts

The application build artifacts were made available for deployment activities. Initial verification was performed to ensure the application was functioning correctly before proceeding with Dockerization and cloud deployment.

Application runs on:

```text
http://localhost:3000
```

---

# Step 2: Dockerization

## Create Dockerfile

```dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 3000

CMD ["npm", "start"]
```

## Build Docker Image

```bash
docker build -t trend-app .
```

## Run Docker Container

```bash
docker run -d -p 3000:3000 --name trend-container trend-app
```

## Verify Application

```bash
docker ps
```

Open:

```text
http://localhost:3000
```

---

# Phase 3: DockerHub Integration

## Login to DockerHub

```bash
docker login -u <dockerhub-username>
```

## Tag Image

```bash
docker tag trend-app <dockerhub-username>/trend-app:latest
```

## Push Image

```bash
docker push <dockerhub-username>/trend-app:latest
```
---

# Phase 4: Infrastructure Provisioning using Terraform

## Infrastructure Components

Terraform provisions:

- VPC
- IAM Roles
- Security Groups
- EKS Cluster
- Worker Nodes

## Initialize Terraform

```bash
terraform init
```

## Validate Configuration

```bash
terraform validate
```

## Preview Changes

```bash
terraform plan out=tfplan
```

## Provision Infrastructure

```bash
terraform apply tfplan -auto-approve
```

## Verify Resources

```bash
aws eks list-clusters
```

---

# Phase 5: Configure AWS EKS

## Configure AWS CLI

```bash
aws configure
```

## Update Kubeconfig

```bash
aws eks update-kubeconfig \
--region <region> \
--name <cluster-name>
```

## Verify Cluster

```bash
kubectl get nodes
```

Expected Result:

```text
Nodes should be in Ready state.
```

---

# Phase 6: Kubernetes Deployment

## Deployment Manifest

Create:

```yaml
deployment.yaml
```

Example:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: trend-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: trend-app
  template:
    metadata:
      labels:
        app: trend-app
    spec:
      containers:
      - name: trend-app
        image: <dockerhub-username>/trend-app:latest
        ports:
        - containerPort: 3000
```

---

## Service Manifest

Create:

```yaml
service.yaml
```

Example:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: trend-service
spec:
  selector:
    app: trend-app
  ports:
  - port: 80
    targetPort: 3000
  type: LoadBalancer
```

---

## Deploy Application

```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

## Verify Deployment

```bash
kubectl get deployments
kubectl get pods
kubectl get svc
```

---

# Phase 7: Version Control

## Git Configuration

```bash
git init
git add .
git commit -m "Initial Commit"
```

## Create Repository

Create a GitHub repository and push code.

```bash
git remote add origin <repository-url>

git branch -M main

git push -u origin main
```
---

# Phase 8: Jenkins Setup

## Install Jenkins

Run Jenkins as Docker container:

```bash
docker run -d \
-p 8080:8080 \
-p 50000:50000 \
--name jenkins \
jenkins/jenkins:lts
```

## Required Plugins

Install:

- Docker Pipeline
- Kubernetes
- Git
- GitHub Integration
- Pipeline
- AWS Credentials

---

## Configure GitHub Webhook

GitHub Repository

→ Settings

→ Webhooks

→ Add Webhook

Payload URL:

```text
http://<jenkins-server>:8080/github-webhook/
```

Trigger builds automatically on every commit.

---

# Jenkins Pipeline

## Jenkinsfile

Pipeline stages:

1. Checkout Code
2. Build Application
3. Build Docker Image
4. Push Docker Image
5. Deploy to Kubernetes
6. Verify Deployment

Example Flow:

```text
Checkout
    ↓
Build
    ↓
Docker Build
    ↓
Docker Push
    ↓
Kubernetes Deploy
    ↓
Success
```

---

# CI/CD Workflow

```text
Developer Push
        ↓
GitHub Repository
        ↓
Webhook Trigger
        ↓
Jenkins Pipeline
        ↓
Docker Build
        ↓
DockerHub Push
        ↓
EKS Deployment
        ↓
Kubernetes Service
        ↓
Application Available
```

---

# Validation Steps

## Docker

```bash
docker ps
docker images
```

## Kubernetes

```bash
kubectl get pods
kubectl get deployments
kubectl get svc
```

## Jenkins

Verify:

- Successful Pipeline Execution
- Docker Image Push
- Kubernetes Deployment

---

# Results

Successfully:

- Dockerized the React application.
- Built and pushed image to DockerHub.
- Provisioned AWS infrastructure using Terraform.
- Created AWS EKS cluster.
- Deployed application using Kubernetes manifests.
- Configured Jenkins CI/CD automation.
- Enabled automatic deployment using GitHub Webhooks.

---

# Author
**Raguraaman V M**


**DevOps Deployment Project**

React Application Deployment using Docker, Terraform, AWS EKS, Kubernetes, DockerHub, GitHub, and Jenkins CI/CD.
