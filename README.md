# Eyego App Deployment on AWS EKS

This project demonstrates the deployment of a Node.js application on AWS EKS (Elastic Kubernetes Service) through a CI/CD pipeline using Jenkins. The application is containerized using Docker, pushed to Docker Hub and AWS ECR, and deployed onto Kubernetes with an Ingress resource for external access.

---

## Overview of the Project

The Eyego App is a Node.js application deployed on AWS EKS. The deployment process includes:
- Building a Docker image for the application.
- Pushing the Docker image to Docker Hub and AWS ECR.
- Deploying the application on Kubernetes using a `Deployment` and `Service`.
- Exposing the application externally using an Ingress resource with an AWS Application Load Balancer (ALB).

---

## Prerequisites

Before you begin, ensure you have the following:
1. **AWS Account**: With permissions to create EKS clusters, IAM roles, and ALBs.
2. **EKS Cluster**: A running EKS cluster with worker nodes.
3. **kubectl**: Installed and configured to access the EKS cluster.
4. **Jenkins**: Installed and configured with necessary plugins (e.g., Docker, Kubernetes, AWS pipeline).
5. **Docker**: Installed on your EC2 instance and Jenkins server.
6. **AWS CLI**: Installed and configured with credentials.

---

## Setup Instructions

### 1. Kubernetes Cluster Setup
1. **Create an EKS Cluster**:
   Use `eksctl` to create a cluster:
   ```bash
   eksctl create cluster --name demo-eks --region us-east-1 --nodegroup-name my-nodes --node-type t2.micro --nodes 2

   Install AWS Load Balancer Controller:
Follow the official guide to install the controller.

2. Jenkins Pipeline Setup
Create a Jenkins Pipeline:
Define a Jenkins pipeline to build, test, and deploy the application using Groovy programming language. Example Jenkinsfile:
pipeline {
    agent any
    stages {
        stage('Preparation') {
            steps {
                checkout scm
            }
        }
        stage('Test') {
            steps {
                sh 'npm test'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t eyego-app:latest .'
            }
        }
        stage('Push to Docker Hub') {
            steps {
                sh 'docker tag eyego-app:latest <your-dockerhub-username>/eyego-app:latest'
                sh 'docker push <your-dockerhub-username>/eyego-app:latest'
            }
        }
        stage('Push to AWS ECR') {
            steps {
                sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin <your-aws-account-id>.dkr.ecr.us-east-1.amazonaws.com'
                sh 'docker tag eyego-app:latest <your-aws-account-id>.dkr.ecr.us-east-1.amazonaws.com/eyego-repo:latest'
                sh 'docker push <your-aws-account-id>.dkr.ecr.us-east-1.amazonaws.com/eyego-repo:latest'
            }
        }
        stage('Deploy to AWS EKS') {
            steps {
                sh 'kubectl apply -f deployment.yaml'
                sh 'kubectl apply -f service.yaml'
                sh 'kubectl apply -f ingress.yaml'
            }
        }
    }
}

3. Application Deployment
Deployment File:
Example deployment.yaml:
apiVersion: apps/v1
kind: Deployment
metadata:
  name: eyego-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: eyego-app
  template:
    metadata:
      labels:
        app: eyego-app
    spec:
      containers:
      - name: eyego-app
        image: <your-aws-account-id>.dkr.ecr.us-east-1.amazonaws.com/eyego-repo:latest
        ports:
        - containerPort: 3000
       
Service File:
Example service.yaml:
apiVersion: v1
kind: Service
metadata:
  name: eyego-app-service
spec:
  type: LoadBalancer
  ports:
  - port: 80
    targetPort: 3000
  selector:
    app: eyego-app

Ingress File:
Example ingress.yaml:
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: eyegoapp-ingress
  annotations:
    kubernetes.io/ingress.class: alb
    alb.ingress.kubernetes.io/scheme: internet-facing
    alb.ingress.kubernetes.io/target-type: ip
spec:
  rules:
    - host: eyegotask.example.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: eyego-app-service
                port:
                  number: 80

Accessing the Application
Once deployed, access the application using the ALB’s DNS name:
http://a0f7435df6fd641258a1614d03c169af-1938563331.us-east-1.elb.amazonaws.com
