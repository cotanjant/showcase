# Showcase Infrastructure & DevOps

This repository showcases my DevOps and infrastructure practices, focusing on Infrastructure as Code (IaC), GitOps deployment pipelines, self-hosted storage, and Kubernetes networking.

The Go application in `backend/` serves as the workload for testing and validating this deployment setup.

## Infrastructure Architecture

The core of this repository lives in `ops/tofu`, where OpenTofu provisions the underlying cluster resources and deployments:

- **GitOps Continuous Delivery**: ArgoCD deployed via Helm (`10.7.1`) manages applications with automated sync, pruning, and self-healing linked to the `showcase-gitops` repository.
- **Database Provisioning**: PostgreSQL provisioned via Bitnami Helm charts (`18.8.13`) with persistent storage and auto-generated credentials managed as Kubernetes secrets.
- **S3 Object Storage**: Self-hosted Garage S3 storage deployed via Helm, paired with an OpenTofu provisioner script that automatically provisions bucket keys, sets bucket permissions, and binds credentials into application secrets.
- **Ingress & TLS**: Traefik ingress routing configured for HTTP/HTTPS services, including TCP passthrough for ArgoCD (`argocd.local`) and HTTP routing for the backend application (`showcase.local`).
- **Container Build**: Multi-stage Dockerfile compiling a static Go binary into a scratch container.

## Stack & Components

| Component | Tool / Technology | Path / Details |
| --- | --- | --- |
| Infrastructure as Code | OpenTofu | `ops/tofu/` |
| GitOps Engine | ArgoCD | `ops/tofu/argocd.tf`, `ops/tofu/argocd-showcaseapp.tf` |
| Object Storage | Garage S3 | `ops/tofu/garage.tf`, `ops/tofu/garage-bucket.tf` |
| Database | PostgreSQL (Bitnami Helm) | `ops/tofu/postgres.tf` |
| Ingress Controller | Traefik | `ops/tofu/showcase-ingress.tf` |
| Containerization | Docker (Multi-stage) | `Dockerfile` |

## Deployment Workflow

1. **Provision Cluster Services**
   OpenTofu applies Helm charts for ArgoCD, PostgreSQL, and Garage S3, then configures S3 buckets and credentials:
   ```bash
   cd ops/tofu
   tofu init
   tofu apply
   ```

2. **GitOps Synchronization**
   ArgoCD watches the GitOps repo (`https://github.com/CANUreal/showcase-gitops.git`) and automatically syncs the Kubernetes manifests to the `showcase` namespace.

3. **Application Workload**
   The Go backend handles simple session management and image upload workflows to verify PostgreSQL connectivity and Garage S3 operations.
