# Eyego App Deployment on AWS EKS

This project demonstrates the deployment of a Node.js application on AWS EKS (Elastic Kubernetes Service) through a CI/CD pipeline using Jenkins.
The application is containerized using Docker, pushed to Docker Hub and AWS ECR, and deployed onto Kubernetes with an Ingress resource for external access.


# Overview of the Project

The Eyego App is a Node.js application on AWS EKS. The deployment is as follows:
- Building a Docker image for the application.
- Pushing the Docker image to Docker Hub and AWS ECR.
- Deployment and Service to run the application on Kubernetes.
- Externalizing the application via an Ingress resource with an AWS Application Load Balancer (ALB).


# App prerequisites

1. AWS Account: With permissions to create EKS clusters, IAM roles, and ALBs.
2. EKS Cluster: An EKS cluster running with worker nodes.
3. kubectl: Installed and configured to access the EKS cluster.
4. Jenkins: Installed and configured with necessary plugins (e.g., Docker, Kubernetes, AWS pipeline).
5. Docker: Installed on your EC2 instance and Jenkins server.
6. AWS CLI: Installed and configured with credentials.


# Setup Instructions

1- Kubernetes Cluster Setup
 Create an EKS Cluster:
   - Use 'eksctl' to create a cluster:
     bash
     eksctl create cluster --name demo-eks --region us-east-1 --nodegroup-name my-nodes --node-type t2.micro --nodes 2



2- Install AWS Load Balancer Controller:
   - Follow the [official guide](https://docs.aws.amazon.com/eks/latest/userguide/aws-load-balancer-controller.html) to install the controller.




# Jenkins Pipeline Setup:

1. Create a Jenkins Pipeline:
- Define a Jenkins pipeline to build, test, and deploy the application using Groovy programming language.
steps:
 - preparation using checkout scm.
 - test to ensure there is nothing missing.
 - build the docker image
 - push Image to docker hub
 - Push the docker image to ECR.
 - Deploy to AWS EKS from the ECR image. 
	















