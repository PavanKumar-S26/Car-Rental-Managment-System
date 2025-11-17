# Car Rental Management System - Complete Deployment Guide

## Project Structure

```
carrentalbackend/          # Spring Boot backend service
├── Dockerfile             # Multi-stage Docker build for backend
├── .dockerignore
├── pom.xml
├── src/
└── ...

carrentalfrontend/         # React/Vite frontend service
├── Dockerfile             # Multi-stage Docker build for frontend
├── .dockerignore
├── package.json
├── src/
└── ...

helm/carrental/            # Helm chart for Kubernetes deployment
├── Chart.yaml             # Chart metadata
├── values.yaml            # Configurable deployment parameters
├── README.md              # Helm deployment documentation
└── templates/
    ├── _helpers.tpl
    ├── backend-configmap.yaml
    ├── backend-deployment.yaml
    ├── backend-secret.yaml
    ├── backend-service.yaml
    ├── backend-ingress.yaml
    ├── frontend-configmap.yaml
    ├── frontend-deployment.yaml
    ├── frontend-service.yaml
    └── frontend-ingress.yaml
```

## Step 1: Build Docker Images

### Backend
```powershell
cd carrentalbackend
docker build -t carrentalbackend:1.0 .
docker tag carrentalbackend:1.0 yourusername/carrentalbackend:1.0
docker push yourusername/carrentalbackend:1.0
```

### Frontend
```powershell
cd ../carrentalfrontend
docker build -t carrentalfrontend:1.0 .
docker tag carrentalfrontend:1.0 yourusername/carrentalfrontend:1.0
docker push yourusername/carrentalfrontend:1.0
```

## Step 2: Deploy to Kubernetes using Helm

### Install Helm Chart
```bash
# Create namespace
kubectl create namespace carrental

# Install chart
helm install carrental ./helm/carrental \
  --namespace carrental \
  --set backend.image.repository=yourusername/carrentalbackend \
  --set backend.image.tag=1.0 \
  --set frontend.image.repository=yourusername/carrentalfrontend \
  --set frontend.image.tag=1.0
```

### Verify Deployment
```bash
# Check pods
kubectl get pods -n carrental

# Check services
kubectl get svc -n carrental

# Check deployments
kubectl get deployments -n carrental

# View logs
kubectl logs -n carrental deployment/backend
kubectl logs -n carrental deployment/frontend
```

## Step 3: Access Services

### Port Forward Frontend
```bash
kubectl port-forward svc/frontend-service 3000:80 -n carrental
# Open browser: http://localhost:3000
```

### Port Forward Backend
```bash
kubectl port-forward svc/backend-service 8080:8080 -n carrental
# API: http://localhost:8080
```

## Step 4: Configure and Customize

Edit `helm/carrental/values.yaml` to customize:
- Replica counts
- Resource limits
- Image repositories and tags
- Environment variables
- Database credentials
- JWT secrets

Example custom deployment:
```bash
helm install carrental ./helm/carrental \
  --namespace carrental \
  -f custom-values.yaml
```

## Step 5: Push to GitHub and Submit

### Setup Git Repositories

If not already initialized:
```powershell
cd c:\devops\car rental managment system\

# For existing repos, ensure remote is set correctly
git remote -v
git remote set-url origin https://github.com/yourusername/carrentalbackend.git  # for backend
git remote set-url origin https://github.com/yourusername/carrentalfrontend.git # for frontend
```

### Commit Changes
```powershell
# Navigate to each project directory and commit

# Backend
cd carrentalbackend
git add .
git commit -m "Add Dockerfile with Kubernetes health checks and Helm chart support"
git push origin main

# Frontend
cd ../carrentalfrontend
git add .
git commit -m "Add Dockerfile and Docker ignore for containerization"
git push origin main

# Helm Chart
cd ../helm
git add .
git commit -m "Add complete Helm chart for Car Rental Management System deployment"
git push origin main
```

### Submission URLs

After pushing to GitHub, provide these URLs:
1. **Frontend Repo**: https://github.com/yourusername/carrentalfrontend
2. **Backend Repo**: https://github.com/yourusername/carrentalbackend
3. **Helm Chart Repo**: https://github.com/yourusername/carrentalbackend/tree/main/helm/carrental
   (or create separate repo for helm charts)

## Helm Chart Features

### ✅ Configured Features
- [x] Multi-service deployment (Frontend + Backend)
- [x] Configurable replicas
- [x] Configurable images and tags
- [x] Configurable ports
- [x] ConfigMaps for application configuration
- [x] Secrets for sensitive data
- [x] Resource limits and requests
- [x] Health checks (liveness, readiness, startup)
- [x] LoadBalancer services
- [x] Ingress templates (disabled by default)
- [x] Helper templates
- [x] Namespace support

### 📊 Configuration Hierarchy
1. `values.yaml` - Default values
2. `-f custom-values.yaml` - Custom values file
3. `--set key=value` - Command line overrides

### 🔄 Common Operations

```bash
# Validate chart
helm lint ./helm/carrental

# Dry run
helm install carrental ./helm/carrental --dry-run --debug

# Template rendering
helm template carrental ./helm/carrental

# Update deployment
helm upgrade carrental ./helm/carrental -n carrental

# Rollback
helm rollback carrental 1 -n carrental

# Uninstall
helm uninstall carrental -n carrental
```

## Docker Configuration Summary

### Backend Dockerfile
- Multi-stage build with Maven 3.9 and Eclipse Temurin 21
- Cache optimization for faster rebuilds
- Health check for Kubernetes probes
- Environment variable support
- Exposes ports 8080 and 8081

### Frontend Dockerfile
- Multi-stage build with Node.js 18
- Production-optimized build output
- Serve utility for SPA hosting
- Health check for Kubernetes probes
- Exposes port 3000

## Troubleshooting

### Pod not starting
```bash
kubectl describe pod <pod-name> -n carrental
kubectl logs <pod-name> -n carrental
```

### Image not found
- Ensure images are pushed to registry
- Verify image names and tags in values.yaml
- Check image pull secrets if using private registry

### Service not accessible
```bash
kubectl get svc -n carrental
kubectl get endpoints -n carrental
```

### Update ConfigMap/Secret
```bash
kubectl rollout restart deployment/backend -n carrental
kubectl rollout restart deployment/frontend -n carrental
```

## Resources Allocated

| Component | CPU Limit | CPU Request | Memory Limit | Memory Request |
|-----------|-----------|------------|--------------|----------------|
| Frontend  | 500m      | 250m       | 512Mi        | 256Mi          |
| Backend   | 1000m     | 500m       | 1024Mi       | 512Mi          |

Total: 1500m CPU, 1536Mi Memory

## Next Steps

1. ✅ Dockerfiles created and enhanced
2. ✅ Helm chart with templates created
3. ✅ values.yaml with all configurations
4. ⏭️ Push to GitHub repositories
5. ⏭️ Submit URLs to provided link

## Important Notes

- Update database credentials in values.yaml before deployment
- Update JWT secret in backend configuration
- Ensure Kubernetes cluster has sufficient resources
- For production, use private Docker registry
- Consider using ConfigMap/Secret management tools
- Implement persistent volumes for data storage

## Support Files

- `helm/carrental/README.md` - Detailed Helm deployment guide
- `helm/carrental/Chart.yaml` - Helm chart metadata
- `helm/carrental/values.yaml` - All configurable parameters
- `carrentalbackend/Dockerfile` - Backend containerization
- `carrentalfrontend/Dockerfile` - Frontend containerization
