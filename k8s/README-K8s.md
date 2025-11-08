Event Management — Kubernetes deployment
=====================================

This folder contains Kubernetes manifests and a CI/CD workflow template to deploy the Event Management Fullstack application.

What is included
- `namespace.yaml` — dedicated `event-management` namespace
- `backend-deployment.yaml` — Deployment + Service for the Spring Boot backend (replicas, probes, resources)
- `frontend-deployment.yaml` — Deployment + Service for the frontend (NGINX or static server)
- `backend-hpa.yaml` — Horizontal Pod Autoscaler for the backend
- `ingress.yaml` — Ingress resource (nginx ingress) with TLS placeholder

Before you apply
1. Build and push images for backend and frontend to a container registry (Docker Hub, GitHub Container Registry, ECR, etc.).
   - Replace `REPLACE_WITH_REGISTRY/event-backend:latest` and `REPLACE_WITH_REGISTRY/event-frontend:latest` in the manifests with your image paths.
2. Create necessary Kubernetes Secrets (DB credentials, any API keys) and a TLS certificate (or install cert-manager).
3. Install an ingress controller (e.g., nginx-ingress) and cert-manager if you want automatic TLS.

Quick manual steps (example using Docker Hub)

1) Build images locally
```powershell
cd backend
docker build -t yourdockerhubuser/event-backend:latest .
cd ../frontend
docker build -t yourdockerhubuser/event-frontend:latest -f Dockerfile.frontend .
```

2) Push images
```powershell
docker push yourdockerhubuser/event-backend:latest
docker push yourdockerhubuser/event-frontend:latest
```

3) Replace image names in YAMLs (or keep them parameterized and use kustomize/helm)

4) Apply manifests
```powershell
kubectl apply -f k8s/namespace.yaml
kubectl apply -f k8s/backend-deployment.yaml
kubectl apply -f k8s/frontend-deployment.yaml
kubectl apply -f k8s/backend-hpa.yaml
kubectl apply -f k8s/ingress.yaml
```

GitHub Actions / CI notes
- A sample workflow is provided at `.github/workflows/ci-cd.yaml`. Configure repository secrets:
  - `REGISTRY` (e.g., ghcr.io or docker.io)
  - `REGISTRY_USERNAME` / `REGISTRY_PASSWORD` (or use GitHub's token for ghcr)
  - `KUBE_CONFIG` (base64-encoded kubeconfig to allow kubectl apply from workflow)

Security & next steps
- Use Kubernetes Secrets for DB credentials; do not store secrets in plain YAML.
- Consider using a managed database (RDS, Cloud SQL) and a StatefulSet only if you must run a DB in-cluster.
- Add PodDisruptionBudgets for higher availability during maintenance.
- Convert manifests to Helm charts or Kustomize overlays for environments (dev/staging/prod).
