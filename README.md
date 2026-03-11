### 📦 Node.js Application CI/CD Pipeline with EKS, Terraform, Prometheus & Grafana

    This project demonstrates a complete DevOps pipeline that provisions infrastructure using Terraform, deploys a Node.js application on Amazon EKS, and implements full CI/CD with GitHub Actions including security scanning and monitoring using Prometheus and Grafana.
    
    The system provisions two main servers:
    
    EKS Server → used to create and manage the Kubernetes cluster
    
    Monitoring Server → runs Prometheus, Grafana, and Node Exporter for observability

## 🏗 Architecture Overview

    GitHub Repository
            │
            │ Push Code
            ▼
    GitHub Actions CI/CD Pipeline
            │
            ├── SonarQube Code Analysis
            ├── Trivy Security Scan
            ├── Build Docker Image
            ├── Push Image to DockerHub
            ├── Update Kubernetes Deployment
            ▼
    EKS Cluster (AWS)
            │
            ├── Node.js Application Pods
            ├── Kubernetes Service (LoadBalancer)
            ▼
    Monitoring Server
            │
            ├── Prometheus
            ├── Node Exporter
            └── Grafana Dashboards

## 🚀 Infrastructure Provisioning (Terraform)

    Terraform provisions two EC2 instances:
    
    Server	Purpose
    EKS Server --> Creates and manages the Kubernetes cluster
    Monitoring Server -->	Runs Prometheus + Grafana

    Initialize Terraform 
        terraform init
    Plan And Apply Infrastructure
        terraform plan && terraform apply --auto-approve

    This will create:

        eks-server
        
        monitoring-server
        
        Security groups
        
        Root volumes

## 📊 Monitoring Server Setup

    Configure Prometheus
    
    Edit configuration:
        cd /etc/prometheus/
        sudo nano prometheus.yml

    Add Node Exporter job:
        - job_name: 'node_exporter'
          static_configs:
            - targets: ['MONITORING_SERVER_IP:9100']
            
    Check configuration:
        promtool check config /etc/prometheus/prometheus.yml

    Reload Prometheus configuration:
        curl -X POST http://localhost:9090/-/reload

## Grafana Setup
    Access Grafana:
        http://MONITORING_SERVER_IP:3000

    Steps:

        Add Prometheus Data Source
        
        Import dashboard : 1860

## ☸️ EKS Cluster Setup

    Attach an IAM Role to the EKS server with required permissions.

    Create the cluster:
        eksctl create cluster \
        --name thumama-cluster \
        --region us-east-1 \
        --node-type t2.small \
        --nodes 3

    Verify cluster:
        kubectl get nodes

## 📈 Kubernetes Monitoring (Prometheus Stack)

    Add Helm repositories:
        helm repo add stable https://charts.helm.sh/stable
        helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

    Create namespace:
        kubectl create namespace prometheus

    Install monitoring stack:
        helm install stable prometheus-community/kube-prometheus-stack -n prometheus

    Verify installation: 
        kubectl get pods -n prometheus
        kubectl get svc -n prometheus

    Expose Prometheus:
        kubectl edit svc stable-kube-prometheus-sta-prometheus -n prometheus

    Change:
        type: LoadBalancer
        port: 9090
        
    Check service:
        kubectl get svc -n prometheus --> copy the LOAD_BALANCER_DNS then access http://LOAD_BALANCER_DNS:9090/metrics to verify it works fine

## 📊 Grafana Kubernetes Dashboards

    In Grafana add the EKS Prometheus endpoint as a data source.

    Import Kubernetes dashboards:
        Kubernetes Cluster Monitoring ID:	15760
        Kubernetes Pod Monitoring ID:	17119

## ⚙️ CI/CD Pipeline (GitHub Actions)
    Pipeline triggers on:
        push → main branch
        pull request events

   # 🔍 Pipeline Stages
        1️⃣ SonarQube Code Analysis

            Performs static code analysis to detect:
            
            Bugs
            
            Vulnerabilities
            
            Code smells

        2️⃣ Dependency Installation
            npm install

        3️⃣ Trivy Filesystem Scan
            Scans the repository for vulnerabilities.

        4️⃣ Docker Image Build 

        5️⃣ Container Security Scan

        6️⃣ DockerHub Push

        7️⃣ Update Kubernetes Deployment

        8️⃣ Inject Kubernetes Manifests to EKS Server

        9️⃣ Remote Deployment

## ☸️ Kubernetes Resources
    Deployment

        2 replicas
        
        Container port 3000
        
        Uses imagePullSecrets
    
    Service
    
        Type:
        
        LoadBalancer
        
        Ports:
        
        80 → 3000

## 🔐 GitHub Secrets

    Required secrets:
    
        SONAR_TOKEN
        DOCKER_USERNAME
        DOCKER_PASSWORD
        REACT_APP_RAPID_API_KEY
        EC2_PRIVATE_KEY
        EC2_USERNAME
        EC2_IP_ADDRESS

## 🛠 Technologies Used

        Terraform
        
        AWS EC2
        
        Amazon EKS
        
        Docker
        
        Kubernetes
        
        Helm
        
        Prometheus
        
        Grafana
        
        GitHub Actions
        
        SonarQube
        
        Trivy

## 🔎 Verification

    Check application:
    
        kubectl get svc
        
        Access via LoadBalancer DNS.
    
    Check monitoring:
    
        Grafana → dashboards
        Prometheus → targets

    
