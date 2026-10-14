# K8s-multi-tier-app

A secure, production-ready three-tier web application architecture deployed on Kubernetes (Kind). This repository demonstrates a completely decoupled microservices design utilizing an Nginx frontend, a Python Flask backend, and a MySQL database, tightly secured using native Kubernetes NetworkPolicies.

## 🏗️ Architecture & Traffic Flow

The infrastructure enforces a strict tier-based communication pattern to ensure zero-trust network isolation:

1. **Frontend Tier (Nginx):** Serves static content and acts as a reverse proxy, routing incoming `/api` traffic to the backend service.
2. **Backend Tier (Flask):** Processes core application logic and queries the database.
3. **Database Tier (MySQL):** Securely stores persistent data and rejects all direct external connections.

## 🔒 Security & Network Isolation

By default, Kubernetes allows all pods to communicate openly. To enforce the principle of least privilege, this project applies dedicated `NetworkPolicy` rules:

* **Frontend Policy:** Receives ingress from anywhere (public traffic); allows egress *only* to the backend tier.
* **Backend Policy:** Accepts ingress *only* from the frontend tier; allows egress *only* to the database tier on port `3306`.
* **Database Policy:** Accepts ingress *only* from the backend tier; completely drops any direct communication attempts from the frontend or unauthorized pods.

## 🚀 Deployment Guide

### Prerequisites
* Docker
* Kind (Kubernetes in Docker)
* kubectl

### 1. Provision the Environment
```bash
# Create the namespace
kubectl create namespace multi-app
kubectl create secret generic database-secret \
> --from-literal=MYSQL_ROOT_PASSWORD=rootpassword123 \
> --from-literal=MYSQL_PASSWORD=userpassword123 \
> -n multi-app
