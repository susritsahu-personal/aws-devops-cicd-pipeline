# AWS DevOps CI/CD Pipeline

A hands-on CI/CD pipeline project that automates the deployment of a Dockerized web application from GitHub to AWS EC2 using Jenkins and AWS Systems Manager.

## Project Overview

This project demonstrates an end-to-end CI/CD workflow where a code change pushed to GitHub automatically triggers Jenkins. Jenkins verifies AWS access and uses AWS Systems Manager (SSM) to deploy the updated application to an Amazon EC2 instance.

The application runs inside a Docker container using Nginx on Amazon Linux.

## CI/CD Workflow

```
Developer
   │
   ▼
GitHub
   │
   │ SCM Polling
   ▼
Jenkins
   │
   ├── Verify AWS credentials
   │
   ├── Connect to EC2 through AWS SSM
   │
   └── Trigger deployment
   │
   ▼
AWS EC2
   │
   ├── Git pull
   ├── Docker build
   ├── Replace running container
   └── Start updated container
   │
   ▼
Docker + Nginx
   │
   ▼
Web Application
```
Technologies Used
- Git
- GitHub
- Jenkins
- Docker
- AWS EC2
- AWS Systems Manager (SSM)
- AWS IAM
- Linux
- Nginx
- AWS CLI
AWS Infrastructure
- EC2: Amazon Linux 2023 running the web application
- IAM: Permissions for Jenkins to use AWS Systems Manager
- Systems Manager: Used for remote deployment without SSH
- Security Group: Allows HTTP traffic on port 80
Deployment Process
1. Application source code is maintained in GitHub.
2. Jenkins monitors the GitHub repository for changes.
3. A new commit automatically triggers the Jenkins pipeline.
4. Jenkins verifies its AWS identity.
5. Jenkins checks the EC2 instance's SSM connectivity.
6. Jenkins sends deployment commands to EC2 through AWS Systems Manager.
7. EC2 pulls the latest code from GitHub.
8. Docker builds a new application image.
9. The previous application container is replaced.
10. A new Docker container is started on port 80.
11. The updated application becomes available through the EC2 public endpoint.
Application
The web application is a simple Nginx-based HTML application running inside Docker.
The Docker container exposes port 80 and serves the application through Nginx.
Jenkins Pipeline
The Jenkins pipeline is defined in a Jenkinsfile stored in the GitHub repository.
The pipeline performs:
- AWS identity verification
- EC2 SSM connectivity verification
- Remote deployment through AWS Systems Manager
- Git pull on EC2
- Docker image build
- Docker container replacement
- Deployment status verification
Key Learning Outcomes
This project provided hands-on experience with:
- Git-based source control
- GitHub repository management
- Jenkins CI/CD pipelines
- SCM-triggered automated builds
- Docker image and container management
- AWS EC2 deployment
- AWS Systems Manager
- AWS IAM permissions
- Linux server administration
- Nginx web serving
- AWS CLI
- Automated application deployment
```text
Project Structure

aws-devops-cicd-pipeline/
│
├── Dockerfile
├── Jenkinsfile
├── index.html
└── README.md

Project Result
A code change pushed to GitHub can automatically trigger Jenkins and deploy the updated Dockerized application to AWS EC2.
The project demonstrates a complete CI/CD deployment workflow using GitHub, Jenkins, Docker, AWS EC2 and AWS Systems Manager.
```
