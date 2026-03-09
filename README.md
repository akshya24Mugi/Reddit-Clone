# Reddit Clone CI/CD Deployment on AWS EKS using Jenkins, ArgoCD, Prometheus & Grafana
## Project Overview

This project demonstrates an end-to-end DevOps pipeline for deploying a Reddit Clone application using modern DevOps practices and tools.

The goal of this project is to automate the build, analysis, containerization, monitoring, and deployment of the application using a CI/CD pipeline integrated with GitOps.

The pipeline includes:
- Infrastructure provisioning using Terraform
- Continuous Integration using Jenkins
- Code Quality Analysis using SonarQube
- Containerization using Docker
- Kubernetes deployment using AWS EKS
- Continuous Deployment using ArgoCD (GitOps)
- Monitoring using Prometheus and Grafana
- Image scanning using Trivy

---

## Tools and Technologies used:
| Tool       | Purpose                      |
| ---------- | ---------------------------- |
| Terraform  | Infrastructure Provisioning  |
| AWS EC2    | Jenkins Server               |
| Jenkins    | Continuous Integration       |
| SonarQube  | Code Quality Analysis        |
| Docker     | Containerization             |
| DockerHub  | Container Registry           |
| Kubernetes | Container Orchestration      |
| AWS EKS    | Managed Kubernetes Cluster   |
| ArgoCD     | GitOps Continuous Deployment |
| Prometheus | Monitoring                   |
| Grafana    | Visualization & Metrics      |

Will add the Architecture diagram here.

---


## Project Implementation phases

---

## Phase 1 -  Infrastructure provisioning using Terraform

The first phase involved launching an AWS EC2 instance using Terraform automation. Terraform was used to create: 
- EC2 Instance
- Security Group
- Network Configuration

The "main.tf" file contains the terraform script and the details about the instance and the security group.
