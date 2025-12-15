# Boutique Microservices - CI/CD Pipeline Overview

This project implements a microservices-based e-commerce application (Boutique) with a fully automated CI/CD pipeline using GitLab CI/CD, Docker, Helm, and ArgoCD on Kubernetes.

## CI/CD Workflow

### 1. Source Code Management
- Each microservice (e.g., frontend, adservice, cartservice, etc.) has its own directory and `.gitlab-ci.yml` pipeline file.
- Source code is managed in GitLab repositories.

### 2. Continuous Integration (CI)
- On every push or merge to the `main` branch, GitLab CI/CD pipeline is triggered for the affected microservice.
- **Build Stage:**
  - The pipeline builds a Docker image for the microservice using the provided Dockerfile.
  - The image is tagged with the commit SHA for traceability.
- **Push Stage:**
  - The built image is pushed to a Docker registry (e.g., Docker Hub or a private registry).

### 3. Helm Chart Update
- After pushing the image, the pipeline automatically clones the Helm charts repository.
- The pipeline updates the corresponding `values.yaml` file for the microservice to reference the new image tag.
- The updated Helm chart is committed and pushed back to the Helm repository (using a CI bot account).

### 4. Continuous Deployment (CD) with ArgoCD
- ArgoCD is configured to watch the Helm charts repository for changes.
- When a new image tag is detected in `values.yaml`, ArgoCD automatically syncs and deploys the updated microservice to the Kubernetes cluster.
- Deployment is performed in the `boutique` namespace.

### 5. Health Checks & Monitoring
- Each service exposes health endpoints for Kubernetes liveness/readiness probes.
- Monitoring and logging are integrated using Prometheus, Grafana, and Loki.

## Key Features
- **Automated Build, Test, and Deploy:** No manual steps required from code commit to production deployment.
- **Traceable Deployments:** Every deployment is linked to a specific Git commit and Docker image tag.
- **Rollback Support:** ArgoCD enables easy rollback to previous versions if needed.
- **Secure Image Handling:** Docker registry credentials and GitLab tokens are managed as CI/CD secrets.

## Directory Structure
```
Boutique/
  ├── frontend/
  ├── adservice/
  ├── cartservice/
  ├── checkoutservice/
  ├── currencyservice/
  ├── emailservice/
  ├── loadgenerator/
  ├── paymentservice/
  ├── productcatalogservice/
  ├── recommendationservice/
  ├── shippingservice/
  └── ...
```

Each service contains its own source code, Dockerfile, and `.gitlab-ci.yml` pipeline definition.

## Getting Started
1. Clone the repository and configure your GitLab CI/CD variables (Docker registry, GitLab token, etc.).
2. Ensure ArgoCD is installed and configured to watch the Helm charts repository.
3. Push code changes to any microservice directory to trigger the pipeline.
4. Monitor deployments and logs via ArgoCD and Grafana dashboards.

## References
- [GitLab CI/CD Documentation](https://docs.gitlab.com/ee/ci/)
- [ArgoCD Documentation](https://argo-cd.readthedocs.io/)
- [Kubernetes Documentation](https://kubernetes.io/docs/)
- [Helm Documentation](https://helm.sh/docs/)
- [Boutique Microservices Example](https://github.com/GoogleCloudPlatform/microservices-demo)
