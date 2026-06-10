<img src="https://capsule-render.vercel.app/api?type=waving&color=0:22d3ee,50:3b82f6,100:8b5cf6&height=180&section=header&text=Online%20Boutique%20DevOps%20Platform&fontSize=36&fontColor=ffffff&animation=fadeIn&fontAlignY=32&desc=GitLab%20CI%20%E2%80%A2%20ArgoCD%20%E2%80%A2%20Helm%20%E2%80%A2%20Prometheus%20%E2%80%A2%20Loki&descAlignY=54&descSize=16" width="100%"/>

<div align="center">

![CI Status](https://img.shields.io/badge/build-passing-brightgreen?style=for-the-badge)
![Kubernetes](https://img.shields.io/badge/kubernetes-v1.28-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white)
![GitOps](https://img.shields.io/badge/gitops-argocd-EF7B4D?style=for-the-badge&logo=argo&logoColor=white)
![Helm](https://img.shields.io/badge/helm-v3-0F1689?style=for-the-badge&logo=helm&logoColor=white)
![License](https://img.shields.io/badge/license-MIT-green?style=for-the-badge)

<img src="https://skillicons.dev/icons?i=kubernetes,gitlab,docker,go,java,cs,py,nodejs,prometheus,grafana&perline=10" alt="Tech stack"/>

</div>

A complete **Cloud-Native DevOps** platform built around Google's [Online Boutique](https://github.com/GoogleCloudPlatform/microservices-demo) — a polyglot microservices e-commerce application. This project automates the full software delivery lifecycle: from a developer's `git push` to a zero-downtime production deployment on Kubernetes, using GitLab CI, ArgoCD, Helm, and a full observability stack.

> **Note:** This repository is a mirror for demonstration and portfolio purposes. The actual system operates against a private self-hosted GitLab instance.

---

## 🎥 Project Demo

[![Watch the Demo](https://img.youtube.com/vi/ycPEOOnVOPg/maxresdefault.jpg)](https://www.youtube.com/watch?v=ycPEOOnVOPg)
*Click the image above to watch the full GitOps & CI/CD workflow demo.*

---

## 🏗️ System Architecture

The platform is built on the **GitOps** principle: the Git repository is the single source of truth for the desired cluster state. ArgoCD continuously reconciles the live Kubernetes cluster against the Helm charts stored in this repo.

### End-to-End Workflow

```mermaid
flowchart TD
    DEV(["👨‍💻 Developer<br/>commits to <code>main</code>"])

    subgraph CI["⚙️ GitLab CI — per-service pipeline"]
        direction TB
        BUILD["🐳 <b>1.</b> Build Docker image"]
        TAG["🏷️ <b>2.</b> Tag with commit SHA"]
        PUSHIMG["📤 <b>3.</b> Push to registry"]
        VALUES["📝 <b>4-6.</b> Update <code>values.yaml</code> image.tag<br/>commit → <i>ci: update values tag SHA</i>"]
        BUILD --> TAG --> PUSHIMG --> VALUES
    end

    HUB[("🐋 Docker Hub<br/><code>bravelove123/ecr</code>")]
    CHARTS[("📚 Git repo<br/><code>devops/boutique-charts</code>")]

    subgraph CD["🐙 ArgoCD — GitOps engine"]
        SYNC["🔄 <b>7.</b> Detects Git change<br/>automated sync · prune · selfHeal"]
        APPLY["⛵ <b>8.</b> Applies Helm chart"]
        SYNC --> APPLY
    end

    subgraph K8S["☸️ Kubernetes — namespace: boutique"]
        ROLL["♻️ Rolling Update<br/><b>zero downtime</b>"]
        PODS["🚀 12 polyglot microservices"]
        ROLL --> PODS
    end

    DEV --> CI
    PUSHIMG -.-> HUB
    VALUES --> CHARTS
    CHARTS --> SYNC
    APPLY --> ROLL
    HUB -.->|"pull image :SHA"| K8S

    classDef dev fill:#22d3ee,stroke:#0e7490,color:#083344,font-weight:bold
    classDef store fill:#1e293b,stroke:#64748b,color:#e2e8f0
    classDef k8s fill:#326CE5,stroke:#1e40af,color:#ffffff
    class DEV dev
    class HUB,CHARTS store
    class ROLL,PODS k8s
```

![Architecture Diagram](architecture-diagram/architecture-diagram.png)

---

## 📁 Repository Structure

```
microservices-demo/
├── Boutique/                        # Microservice source code
│   ├── adservice/                   # Java   – Ad recommendations
│   ├── cartservice/                 # C#     – Shopping cart (Redis-backed)
│   ├── checkoutservice/             # Go     – Order orchestration
│   ├── currencyservice/             # Node.js – Currency conversion
│   ├── emailservice/                # Python – Order confirmation emails
│   ├── frontend/                    # Go     – Web UI (serves HTTP)
│   ├── loadgenerator/               # Python – Locust-based traffic simulation
│   ├── paymentservice/              # Node.js – Payment processing
│   ├── productcatalogservice/       # Go     – Product catalog (gRPC)
│   ├── recommendationservice/       # Python – Product recommendations
│   ├── shippingservice/             # Go     – Shipping quotes & tracking
│   └── shoppingassistantservice/    # Python – AI shopping assistant
│
├── devops/                          # Infrastructure as Code
│   ├── argocd-library/              # ArgoCD AppProject + Application YAMLs
│   │   ├── boutique-project.yaml    # AppProject scoped to `boutique` namespace
│   │   ├── boutique/                # Application YAML per microservice
│   │   ├── log/                     # Application YAMLs for logging stack
│   │   └── monitoring/              # Application YAMLs for monitoring stack
│   ├── boutique-charts/             # Helm charts – one per microservice
│   ├── log-charts/                  # Helm charts – Loki, Promtail, Grafana
│   └── monitoring-charts/           # Helm charts – kube-prometheus-stack
│
└── architecture-diagram/            # System architecture assets
```

---

## 🧩 Microservices

| Service | Language | Role |
| :--- | :---: | :--- |
| **frontend** | Go | Web UI — serves HTTP, calls all backend services |
| **productcatalogservice** | Go | Exposes product list & details via gRPC |
| **cartservice** | C# | Manages shopping cart state in Redis |
| **checkoutservice** | Go | Orchestrates checkout: currency, payment, shipping, email |
| **paymentservice** | Node.js | Processes credit card charges |
| **currencyservice** | Node.js | Converts prices between currencies |
| **shippingservice** | Go | Provides shipping quotes and order tracking |
| **emailservice** | Python | Sends order confirmation emails |
| **recommendationservice** | Python | Returns product recommendations |
| **adservice** | Java | Serves context-aware advertisements |
| **loadgenerator** | Python | Simulates realistic user traffic via Locust |
| **shoppingassistantservice** | Python | AI-powered shopping assistant |

Each service has its own **Dockerfile** and **`.gitlab-ci.yml`** pipeline, enabling fully independent build and deployment cycles.

---

## 🛠️ Tech Stack

| Domain | Technologies |
| :--- | :--- |
| **Orchestration** | Kubernetes (K8s), Helm v3 |
| **CI/CD** | GitLab CI (build & push), ArgoCD (GitOps sync) |
| **Containerization** | Docker, Docker Hub |
| **Metrics & Alerting** | Prometheus, Grafana (kube-prometheus-stack) |
| **Logging** | Loki, Promtail, Grafana |
| **Languages** | Go, Java, C#, Python, Node.js |

---

## 🚀 CI/CD Pipeline — Deep Dive

### Per-Service GitLab CI (`.gitlab-ci.yml`)

Every microservice repository contains a GitLab CI pipeline that fires on every commit to `main`:

```
Stage: build_and_push
  │
  ├─ 1. Build Docker image
  │      docker build -t $DOCKERHUB_REPO:$CI_COMMIT_SHORT_SHA .
  │
  ├─ 2. Push to Docker Hub
  │      docker push $DOCKERHUB_REPO:$CI_COMMIT_SHORT_SHA
  │
  └─ 3. Auto-update Helm chart values
         git clone devops/boutique-charts (shallow)
         sed → image.tag: "<commit-sha>" in values.yaml
         git commit "ci: update values tag <SHA>"
         git push → triggers ArgoCD sync
```

Image tags use the **commit SHA** (`$CI_COMMIT_SHORT_SHA`) to make every deployed artifact fully traceable to its source commit.

### GitOps with ArgoCD

Each microservice has a dedicated ArgoCD `Application` resource (in `devops/argocd-library/boutique/`):

```yaml
syncPolicy:
  automated:
    prune: true       # Remove resources deleted from Git
    selfHeal: true    # Revert manual changes to cluster
```

ArgoCD watches the `boutique-charts` repo. As soon as the CI pipeline pushes an updated `values.yaml`, ArgoCD detects the drift and triggers a **Rolling Update** with no manual intervention.

---

## 📊 Observability Stack

### Metrics (kube-prometheus-stack)
- **Prometheus** — scrapes metrics from all pods and Kubernetes system components
- **Grafana** — dashboards for CPU, memory, request rates, and HPA scaling events
- Deployed via `devops/monitoring-charts/kube-prometheus-stack`

### Logging (Loki stack)
- **Promtail** — DaemonSet agent that ships pod logs to Loki
- **Loki** — log aggregation backend (drop-in for Elasticsearch, label-based indexing)
- **Grafana** — unified interface for both metrics and logs
- Deployed via `devops/log-charts/`

---

## ⚙️ Horizontal Pod Autoscaling (HPA)

Critical services are configured with HPA in their Helm `values.yaml`. Example for `frontend`:

```yaml
autoscaling:
  minReplicas: 1
  maxReplicas: 5
  targetCPUUtilizationPercentage: 60
```

The load generator (`Boutique/loadgenerator/`) uses **Locust** to simulate realistic traffic patterns and validate autoscaling behavior under load.

---

## 🏁 Getting Started

### Prerequisites

| Tool | Version |
| :--- | :--- |
| `kubectl` | ≥ 1.28 |
| `helm` | ≥ 3.12 |
| `argocd` CLI | ≥ 2.8 |
| Kubernetes cluster | ≥ 1.28 |

### 1. Install ArgoCD

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### 2. Apply the ArgoCD AppProject

```bash
kubectl apply -f devops/argocd-library/boutique-project.yaml
```

### 3. Deploy each microservice

```bash
# Apply all ArgoCD Application manifests
kubectl apply -f devops/argocd-library/boutique/
```

ArgoCD will pull the Helm charts from the configured repo and deploy all services to the `boutique` namespace automatically.

### 4. Deploy the Observability Stack

```bash
# Monitoring (Prometheus + Grafana)
kubectl apply -f devops/argocd-library/monitoring/

# Logging (Loki + Promtail + Grafana)
kubectl apply -f devops/argocd-library/log/
```

### 5. Access the Application

```bash
kubectl port-forward svc/frontend 8080:80 -n boutique
# Open http://localhost:8080
```

---

## ✨ Key Highlights

| Feature | Detail |
| :--- | :--- |
| **Automated CI/CD** | Every `main` commit triggers build → push → values update → sync |
| **Immutable image tags** | Docker images tagged by commit SHA — full traceability |
| **Zero-downtime deploys** | Kubernetes Rolling Update strategy on every release |
| **GitOps with self-healing** | ArgoCD reverts any manual cluster drift automatically |
| **Full observability** | Metrics (Prometheus/Grafana) + Logs (Loki/Promtail) in one Grafana UI |
| **Autoscaling** | HPA configured per service, validated with Locust load tests |
| **Polyglot** | 6 languages, 12 services — each independently built and deployed |

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:22d3ee,50:3b82f6,100:8b5cf6&height=100&section=footer" width="100%"/>
