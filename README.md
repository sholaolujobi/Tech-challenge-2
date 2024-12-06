# Jenkins, Docker, and AWS EKS CI/CD Deployment

This project demonstrates how to deploy a web application using Docker, orchestrate it with AWS 
EKS, and set up a continuous deployment pipeline using **Jenkins**. The application is a simple 
Flask-based service that responds with a greeting message.

---

## Features
- Dockerized Flask Application**: The application is containerized for consistent deployment across 
environments.
- Kubernetes Orchestration**: Deployed using an AWS EKS cluster with a `LoadBalancer` service.
- CI/CD Pipeline**: Jenkins automates building, pushing Docker images to Amazon ECR, and deploying to 
the EKS cluster.

---

## Installation Instruction*

### Prerequisites
Ensure you have the following installed:
1. Docker: [Install Docker](https://docs.docker.com/get-docker/)
2. AWS CLI: [Install AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html)
3. eksctl: [Install eksctl](https://eksctl.io/)
4. kubectl: [Install kubectl](https://kubernetes.io/docs/tasks/tools/)
5. Jenkins: Installed locally or on an EC2 instance. [Install 
Jenkins](https://www.jenkins.io/doc/book/installing/)
6. Git: [Install Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)

### Setup AWS CLI
Configure the AWS CLI with your credentials:
```bash
aws configure
```

---

## Project Structure
```plaintext
├── src/                    # Application source code
│   ├── app.py              # Flask application
│   ├── requirements.txt    # Python dependencies
├── Dockerfile              # Dockerfile for the application
├── k8s/                    # Kubernetes manifests
│   ├── deployment.yaml     # Kubernetes deployment manifest
│   ├── service.yaml        # Kubernetes service manifest
├── Jenkinsfile             # Jenkins pipeline definition
├── .gitignore              # Ignored files for Git
└── README.md               # Documentation
```

---

## Usage

### 1. Clone the Repository
Clone the private GitHub repository to your local machine:
```bash
git clone https://github.com/<your-username>/jenkins-docker-eks-deployment.git
cd jenkins-docker-eks-deployment
```

### 2. Build and Test Locally
Build and test the Flask application using Docker:
```bash
docker build -t flask-app .
docker run -p 5000:5000 flask-app
```
Access the application at `http://localhost:5000`.

---

## Deployment

### 1. Push Docker Image to Amazon ECR
Authenticate with Amazon ECR:
```bash
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 
288761770474.dkr.ecr.us-east-1.amazonaws.com
```

Create a repository in Amazon ECR:
```bash
aws ecr create-repository --repository-name flask-app
```

Tag and push the Docker image:
```bash
docker tag flask-app:latest 288761770474.dkr.ecr.us-east-1.amazonaws.com/flask-app:latest
docker push 288761770474.dkr.ecr.us-east-1.amazonaws.com/flask-app:latest
```

### 2. Create an EKS Cluster
Create the EKS cluster using `eksctl`:
```bash
eksctl create cluster --name my-eks-cluster --region us-east-1 --nodes 2
```

### 3. Deploy to Kubernete*
Deploy the application using Kubernetes manifests:
```bash
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml
```

### 4. Verify Deployment
Get the service details to retrieve the LoadBalancer URL:
```bash
kubectl get services
```
Access the application at the LoadBalancer URL.

---

## Jenkins CI/CD Pipeline

### 1. Jenkins Setup
1. Install the following Jenkins plugins:
   - Docker Pipeline
   - Kubernetes CLI
   - GitHub Integration
2. Add AWS and Docker credentials in Jenkins.
3. Create a pipeline job and link it to the repository.

### 2. Pipeline Stages
The pipeline performs the following:
1. Build: Builds the Docker image for the application.
2. Push: Pushes the image to Amazon ECR.
3. Deploy: Deploys the application to the EKS cluster using `kubectl`.

### 3. Trigger the Pipeline
- Automatically triggered by GitHub webhook or manually triggered in Jenkins.

---

## Troubleshooting

### Common Issues:
1. LoadBalancer not created: Check EKS cluster IAM roles and networking.
2. Image pull errors**: Ensure the image is pushed to the correct ECR repository and Kubernetes has 
access.

---

## Future Improvements
- Add health checks and monitoring (e.g., Prometheus and Grafana).
- Use Helm charts for Kubernetes deployments.
- Enhance security with role-based access control (RBAC) in Kubernetes.

---

