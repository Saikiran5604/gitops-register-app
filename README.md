# GitOps Register App — Kubernetes CD

GitOps repository for deploying the **Register App** to **Amazon EKS** using **Argo CD** and Jenkins.

This repository contains the Kubernetes deployment configuration used by the CD pipeline. The application source code and CI pipeline are maintained separately in the [`register-app`](https://github.com/Saikiran5604/register-app) repository.

## Architecture
<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/d151333f-0712-4983-acf2-aa87bf3e5beb" />

---

## GitOps Workflow

The CD process follows a GitOps approach:

```text
CI Build
   ↓
Docker Image
   ↓
IMAGE_TAG
   ↓
Jenkins CD
   ↓
Update deployment.yaml
   ↓
Push to GitHub
   ↓
Argo CD detects change
   ↓
Syncs EKS
   ↓
New application version
```

The GitOps repository acts as the **desired state** for the Kubernetes environment.

## Kubernetes Deployment

The application deployment is defined in `deployment.yaml`.

The image tag is updated automatically by Jenkins during the CD pipeline.

Example:

```yaml
image: saikiranreddy5604/register-app-pipeline:1.0.0-37
```

The build number is included in the image tag so that every deployment can be traced back to a specific CI build.

## Jenkins CD Pipeline

The CD pipeline performs the following operations:

```text
Receive IMAGE_TAG
       ↓
Checkout GitOps Repository
       ↓
Update deployment.yaml
       ↓
Commit Changes
       ↓
Push to GitHub
       ↓
Argo CD Sync
```

Example commands used by the pipeline:

```bash
git add deployment.yaml

git commit -m "Updated Deployment Manifest to tag 1.0.0-37 [skip ci]"

git push origin main
```

## Argo CD

Argo CD continuously monitors this repository and compares the Kubernetes cluster with the configuration stored in Git.

```text
Git Repository
      │
      │ Desired State
      ▼
   Argo CD
      │
      │ Sync
      ▼
   Amazon EKS
```

When `deployment.yaml` changes, Argo CD detects the difference and synchronizes the new configuration to the EKS cluster.

## Kubernetes Service

The application is exposed using a Kubernetes `LoadBalancer` service.

```bash
kubectl get svc
```

Example:

```text
NAME                   TYPE           PORT(S)
register-app-service   LoadBalancer  8080:31782/TCP
```

AWS provisions an Elastic Load Balancer for external access to the application.

Useful commands:

```bash
kubectl get pods
kubectl get pods -o wide
kubectl get deployments
kubectl get svc
kubectl get endpoints register-app-service
```

## EKS & Argo CD Setup

The deployment environment uses:

* **Amazon EKS** — Kubernetes cluster
* **Argo CD** — GitOps continuous delivery
* **Kubernetes Deployment** — Application workload
* **Kubernetes Service** — Application networking
* **AWS Load Balancer** — External access

Argo CD is installed in the EKS cluster and configured to manage the application deployment.

## Repository Relationship

```text
register-app
│
├── Application Source Code
├── Maven Build
├── Tests
├── SonarQube
├── Docker Build
├── Trivy Scan
└── Jenkins CI
        │
        │ IMAGE_TAG
        ▼
gitops-register-app
│
├── Kubernetes Deployment
├── Kubernetes Service
└── Jenkins CD / Argo CD
        │
        ▼
     Amazon EKS
```

Keeping these repositories separate provides a clear separation between **application development** and **deployment configuration**.

## Learning Reference

This project was built by following and adapting concepts from the **Real Time DevOps Project — Deploy to Kubernetes Using Jenkins** tutorial by **Ashfaque-9x / VirtualTechBox**.

[YouTube Tutorial](https://www.youtube.com/watch?v=e42hIYkvxoQ)

The implementation was adapted to use the Register App, Jenkins CI/CD, Docker Hub, GitHub-based GitOps, Argo CD, and Amazon EKS.
