# DevOps Platform: Google Microservices Demo (Online Boutique)

![CI Status](https://img.shields.io/badge/build-passing-brightgreen) ![Kubernetes](https://img.shields.io/badge/kubernetes-v1.28-blue) ![GitOps](https://img.shields.io/badge/gitops-argocd-orange)

This project demonstrates a complete **Cloud-Native DevOps** workflow for deploying a microservices-based e-commerce application on Kubernetes. It automates the lifecycle from code commit to production deployment using modern tools like GitLab CI, ArgoCD, and Helm.

> **Note:** This repository is a mirror for demonstration and portfolio purposes. The actual system allows for hybrid operations using GitLab CI.

## 🏗️ System Architecture

The platform follows the **GitOps** principle, using ArgoCD as the single source of truth for the cluster state.

**Workflow:**
`Developer` ➔ `GitLab CI (Build & Push)` ➔ `Docker Hub` ➔ `Update Helm Chart` ➔ `ArgoCD Sync` ➔ `Kubernetes Cluster`

![Architecture Diagram](DEMO/architecture-diagram.png)

## 🛠️ Tech Stack

| Domain | Technologies / Tools |
| :--- | :--- |
| **Orchestration** | Kubernetes (K8s), Helm Charts |
| **CI/CD** | GitLab CI (Pipelines), ArgoCD (GitOps) |
| **Containerization** | Docker, Docker Hub |
| **Observability** | Prometheus (Metrics), Grafana (Dashboards)|
| **Languages** | Java, C#, Go, Python, Node.js (Polyglot) |

## 🚀 Key Highlights

* **Automated CI/CD Pipeline:** Every commit to the `main` branch triggers a pipeline that builds Docker images, tags them with the specific Commit SHA, and pushes them to the registry.
* **GitOps Deployment:** Utilized **ArgoCD** to automatically synchronize the application state in Kubernetes with the Helm Charts in this repository.
* **Zero-Downtime Updates:** Implemented Rolling Update strategies ensuring the application remains available during deployments.
* **Full Observability Stack:** Integrated **Prometheus & Grafana** for resource monitoring (CPU/RAM).
* **Scalability:** Configured **Horizontal Pod Autoscaling (HPA)** for critical services based on CPU utilization.