# DevOps Project - CI/CD & Infrastructure Overview

This repository contains infrastructure-as-code, CI/CD pipeline definitions, and deployment manifests for managing microservices and platform components in a Kubernetes environment.

## Structure
```
devops/
  ├── argocd-library/         # ArgoCD Application and Project YAMLs
  ├── boutique-charts/        # Helm charts for Boutique microservices
  ├── log-charts/             # Helm charts for logging stack (Loki, Promtail, etc.)
  ├── monitoring-charts/      # Helm charts for monitoring stack (Prometheus, Grafana, etc.)
  ├── ...                     # Other infrastructure or deployment configs
```

## CI/CD Workflow

### 1. GitOps with ArgoCD
- All Kubernetes manifests and Helm charts are managed in Git repositories.
- ArgoCD is used to continuously synchronize the desired state from Git to the Kubernetes cluster.
- Application and project definitions are stored in `argocd-library/` and applied via `kubectl` or ArgoCD UI.

### 2. Helm Chart Management
- Each microservice or platform component has its own Helm chart (in `boutique-charts/`, `log-charts/`, `monitoring-charts/`, etc.).
- Chart updates (e.g., new image tags, config changes) are committed to Git and automatically picked up by ArgoCD for deployment.

### 3. CI/CD Integration
- Microservice source code repos (outside this folder) use GitLab CI/CD to build and push Docker images.
- After a successful build, the pipeline updates the corresponding Helm chart's `values.yaml` with the new image tag and pushes the change to this repository.
- ArgoCD detects the change and deploys the new version to Kubernetes.

### 4. Monitoring & Logging
- Monitoring stack (Prometheus, Grafana) and logging stack (Loki, Promtail) are deployed and managed via Helm charts in this repo.
- Dashboards and alerting rules are version-controlled and can be updated via pull requests.

## How to Use
1. Clone this repository and review the structure.
2. Make changes to Helm charts or manifests as needed, then commit and push.
3. ArgoCD will automatically sync and deploy changes to the cluster.

## Best Practices
- Use pull requests and code review for all infrastructure changes.
- Keep secrets and sensitive values in secure stores (e.g., Kubernetes secrets, external vaults).
- Monitor deployments and cluster health via Grafana dashboards and Prometheus alerts.

## References
- [ArgoCD Documentation](https://argo-cd.readthedocs.io/)
- [Helm Documentation](https://helm.sh/docs/)
- [Kubernetes Documentation](https://kubernetes.io/docs/)
- [Prometheus](https://prometheus.io/)
- [Grafana](https://grafana.com/)
- [Loki](https://grafana.com/oss/loki/)
