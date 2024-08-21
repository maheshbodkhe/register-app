# Real Time DevOps Project | Deploy to Kubernetes Using Jenkins | End to End DevOps Project | CICD

## Project Description

Creating a CI/CD pipeline for a Java application to deploy on a Kubernetes cluster using Jenkins is a complex and multi-step process. In this blog post, we’ll walk through each step in detail, from installing and configuring the necessary tools to deploying the application on Kubernetes. We will also discuss the integration of Jenkins with SonarQube for static code analysis, Dockerizing the application, scan docker image using trivy then update build number in manifest and deploy it on EKS cluster through ArgoCD Let’s get started.

### Workflow Diagram

![workflow](https://github.com/user-attachments/assets/c9951c4e-316c-4305-8d97-b241babd84da)

### Pre-requisites
  - Login to GitHub, Docker Hub and AWS Accouts
  - Launch EC2 instances 3: Jenkins Master, Agent and EKS Bootstrap server
  - T2 Micro for Jenkins master and EKS Bootstrap server and T2.medium for Agent
  - Security Group: create new group with open All traffic- No recommended in Prod environment, its just for practice
  - Default VPC , RAM 15GB
