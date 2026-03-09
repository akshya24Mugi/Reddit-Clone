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
| Trivy      | Container Image Security Scanning |

Will add the Architecture diagram here and also add trivy to scan the image quality

---


## Project Implementation phases

---

## Phase 1 -  Infrastructure provisioning using Terraform

The first phase involved launching an AWS EC2 instance using Terraform automation. Terraform was used to create: 
- EC2 Instance
- Security Group
- Network Configuration

The "main.tf" file contains the terraform script and the details about the instance and the security group.

add the screenshot of the intance here.

---

## Phase 2 — Jenkins Installation & SonarQube Integration

In this phase, Jenkins was installed on the EC2 instance. The steps performed during this phase were:

1. Installed Java (required for jenkins), Sonarqube and Trivy.
2. Installed Jenkins and enabled the Jenkins server.
Add the script used.
3. Accesses Jenkins using EC2 public IP.
4. Installed required Jenkins plugins such as SonarQube scanner, NodeJS, Trivy image scanner, Docker etc.
5. Integrated SonarQube with Jenkins using a generated SonarQube authentication token.

This integration allows Jenkins pipelines to perform code quality analysis during the build process.

Add the sonarQube Screenshot here.

---

## Phase 3 — CI Pipeline Setup

A Jenkins CI Pipeline was created to automate the application build process.

Before creating the pipeline:
- Added the GitHub Personal Access Token to Jenkins Credentials.
- Added the DockerHub Access Token to Jenkins Credentials.

The CI Pipeline performs the following tasks:
1. Pulls source code from GitHub.
2. Builds the application.
3. Runs SonarQube code analysis.
4. Builds the Docker image.
5. Performs a Trivy security scan on the Docker image to detect vulnerabilities.
6. Pushes the Docker image to DockerHub.

The Trivy scan generates a vulnerability report, which helps identify security risks in container images.

---

## Phase 4 — Email Notification with Trivy Report

Jenkins was configured to send email notifications for pipeline results.

Steps performed:
1. Generated a Gmail App Password.
2. Added the Gmail credentials in Jenkins as a credentials.
3. Configured Jenkins Email settings.
4. Added the Email notification stage in the CI pipeline script in the Post stage.

The email sent after the CI pipeline includes:
- Build status (Success / Failure)
- Trivy image scan report
- Pipeline logs summary

This allows quick visibility into build results and security vulnerabilities.

Add the screenshot from the mail here.


---

## Phase 5 -  AWS EKS Cluster setup

An Amazon EKS cluster was created to deploy the containerized application.

Tools installed on the Jenkins server:
- kubectl
- aws-cli
- eksctl

Steps performed:
1. kubectl installation

```-jenkins_server
$ sudo apt update
$ sudo apt install curl
$ curl -LO https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl
$ sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
$ kubectl version --client

```
2. aws-cli installation:
```-jenkins server
$ curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
$ sudo apt install unzip
$ unzip awscliv2.zip
$ sudo ./aws/install
$ aws --version
```
3. eksctl installation:
```
$ curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
$ cd /tmp
$ sudo mv /tmp/eksctl /bin
$ eksctl version
```

After this, we create an EKS cluster on AWS through the CLI:
```
$ eksctl create cluster --name virtualtechbox-cluster \
--region ap-south-1
--node-type t2.small
--nodes 3 \
```
Verify whether the nodes are created or not:
```
$ kubectl get nodes
```
Add the screenshot of the nodes and the cluster here!!

---

Phase 6 -- Monitoring Setup (Prometheus and Grafana)

Cluster monitoring was implemented using the Prometheus and Grafana.

Prometheus collects metrics from the Kubernetes cluster, while Grafana provides virtualized dashboards.

Installation was done using Helm charts.

Steps performed:
- Installed Helm.
- Installed Prometheus using Helm.
- Created a prometheus namespace.

Verified Prometheus pods using:
```
$ kubectl get pods -n prometheus
$ kubectl get svc -n prometheus 

```

After this, I had to expose the Prometheua and Grafana to the outside world.

```
# Change the service type from "ClusterIP" to "LoadBalancer" and the port number from '9000' to '9090'.
$ kubectl edit svc stable-kube-prometheus-sta-prometheus -n prometheus 

# Change the same from Grafana
$ kubectl edit svc stable-grafana -n prometheus

```

Verify all this using:
```
$ kubectl get svc -n prometheus
```

Access the Grafana (using the dns) and Prometheus (using the dns:9090) dashboard.

The dashboards on grafana were imported from promethues data source using the following Grafana Dashboard ID's:
- kubernetes-monitoring - ID 12740
- kubernetes-views-pods - ID 15760

After the dashboards are created, we can monitor data on dashboards which is coming via prometheus.

Add the prometheus and grafana screenshots here!!

---

## Phase 7 — ArgoCD Setup 

Argod was installed to implement the GitOps-Based Continous Deployment (CD) using the Github repo : "https://github.com/akshya24Mugi/"Reddit-Clone-GitOps".

Steps performedL
1. Create a namespace
```
$ kubectl create namespace argocd
```
2. Apply the yaml configuration file for ArgoCD
```
$ kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```
3. Verified the pods creation
```
kubectl get pods -n argocd
```
4. Now we need to deploy the CLI to interact with the API server of ArgoCD and also expose the server to the outside world:
```
$ sudo curl --silent --location -o /usr/local/bin/argocd https://github.com/argoproj/argo-cd/releases/download/v2.4.7/argocd-linux-amd64
$ sudo chmod +x /usr/local/bin/argocd
$ kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
```
5. Verify the LoadBalancer creation
```
$ kubectl get svc -n argocd
```

After this we login through the CLI and ArgoCD web server.

add the screenshot here of the ArgoCD server!

---

## Phase 8 — GitOps Deployment using ArgoCD

ArgoCD was configureed to deploy the application using a GitOps repository containing Kubernetes manifests.

Steps performed:
1. Created an Application on ArgoCD webserver.
2. Connected the GitOps Repository to the ArgoCD server.
3. ArgoCD monitors the repository and automatically syncs deployments with the cluster.

A CD pipeline was also created in Jenkins to make sure that everytime when there is code change in "Reddit-Clone" repo, due to any method, it will trigger the CI/CD pipeline.

Also, one more important thing, we added a webhook in this repo and then added that webhook to the Jenkins server which helps in detecting code change in the repository, which will trigger the CI pipeline and then CD pipeline!!


---

## Final Deployment

The Reddit Clone application is successfully deployed on the AWS EKS cluster and exposed using a LoadBalancer service.
add the screenshot of the REddit home page.
