# Minikube Cluster Bootstrap with FluxCD and NGINX
This repository demonstrates how to create a **local Kubernetes cluster using Minikube**, bootstrap it with **FluxCD (GitOps)**, and deploy **NGINX** as a sample application managed entirely through Git.
---
# Overview

* **Minikube** provides a local Kubernetes environment
* **FluxCD** continuously syncs cluster state from Git
* **NGINX** is deployed declaratively using Kubernetes manifests
* **GitHub** acts as the single source of truth

This setup is ideal for learning and practicing **GitOps workflows** in a local environment.

## Architecture
Developer
   |
   v
git push
   |
GitHub Repository
   |
   v
FluxCD (flux-system namespace)
   |
   v
Minikube Kubernetes Cluster
   |
   v
NGINX Application

## Prerequisites
Make sure the following tools are installed:

* Docker
* Minikube
* kubectl
* Flux CLI
* Git
* GitHub account

Check versions:
minikube version
kubectl version --client
flux --version
docker --version
## Start Minikube Cluster
Start a local Kubernetes cluster using Docker driver:
minikube start --driver=docker
Verify cluster:
kubectl get nodes
## Bootstrap FluxCD

### 1. Export GitHub Token
export GITHUB_TOKEN=<your-github-token>
### 2. Bootstrap Flux into Minikube
flux bootstrap github
  --owner=<github-username> \
  --repository=<repo-name> \
  --branch=main \
  --path=clusters/minikube \
  --personal
  
This will:
* Install Flux components
* Create `flux-system` namespace
* Connect cluster to GitHub
## Repository Structure
.
├── clusters/
│   └── minikube/
│       └── flux-system/
├── nginx/
│   ├── deployment.yaml
│   ├── service.yaml
│   └── kustomization.yaml
└── README.md

## Deploy NGINX using FluxCD

### NGINX Kustomization (Flux)

yaml
apiVersion: kustomize.toolkit.fluxcd.io/v1
kind: Kustomization
metadata:
  name: nginx
  namespace: flux-system
spec:
  interval: 1m
  path: ./nginx
  prune: true
  sourceRef:
    kind: GitRepository
    name: flux-system

Commit and push changes:
# in power shell run
git add .
git commit -m "Deploy NGINX using FluxCD"
git push origin main
## Verify Deployment
Check Flux status:
flux get kustomizations
Check NGINX pods and service:
kubectl get pods
kubectl get svc
# Access NGINX Application

Expose the service using Minikube:
minikube service nginx
This opens the NGINX welcome page in your browser.

---
## Cleanup

To stop or delete the cluster:`
minikube stop
minikube delete

## Key Learnings
* GitOps workflow with FluxCD
* Declarative Kubernetes deployments
* Local cluster management using Minikube
* Automated application reconciliation from Git
## 📖 References

* [https://fluxcd.io](https://fluxcd.io)
* [https://minikube.sigs.k8s.io](https://minikube.sigs.k8s.io)
* [https://kubernetes.io](https://kubernetes.io)
---
Author
Maintained by **Saiganesh Paluri**
If you find this project useful, feel free to star the repository!
