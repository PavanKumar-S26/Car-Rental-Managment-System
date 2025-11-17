# Car Rental Management System - Complete Kubernetes Deployment Solution

## 🎯 Project Overview

This project provides a complete, production-ready Helm chart for deploying a Car Rental Management System with both frontend (React/Vite) and backend (Spring Boot) services on Kubernetes.

## ✅ Task Completion Status

All requested features have been successfully implemented and pushed to GitHub:

| Task | Status | Details |
|------|--------|---------|
| Helm Chart Creation | ✅ Complete | `helm/carrental/` with all templates |
| values.yaml Configuration | ✅ Complete | Fully configurable parameters |
| Frontend Dockerfile | ✅ Complete | Multi-stage Node.js 18 build |
| Backend Dockerfile | ✅ Complete | Enhanced multi-stage Maven/Java build |
| Frontend Templates | ✅ Complete | Deployment, Service, ConfigMap |
| Backend Templates | ✅ Complete | Deployment, Service, ConfigMap, Secret |
| Configurable Images | ✅ Complete | Repository and tag customizable |
| Configurable Ports | ✅ Complete | Frontend (80/3000), Backend (8080) |
| Configurable Replicas | ✅ Complete | Both services (default: 2 replicas) |
| Documentation | ✅ Complete | DEPLOYMENT_GUIDE.md, README.md |
| GitHub Push | ✅ Complete | All changes committed and pushed |

---

## 📁 Project Structure

```
Car-Rental-Managment-System/
├── helm/
│   └── carrental/                          # Main Helm Chart
│       ├── Chart.yaml                      # Chart metadata (v1.0.0)
│       ├── values.yaml                     # Configuration values
│       ├── README.md                       # Helm documentation
│       └── templates/
│           ├── _helpers.tpl               # Template helpers
│           ├── backend-configmap.yaml     # Backend config
│           ├── backend-deployment.yaml    # Backend deployment (2 replicas)
│           ├── backend-secret.yaml        # Backend secrets (DB, JWT)
│           ├── backend-service.yaml       # Backend service (port 8080)
│           ├── backend-ingress.yaml       # Backend ingress (optional)
│           ├── frontend-configmap.yaml    # Frontend config
│           ├── frontend-deployment.yaml   # Frontend deployment (2 replicas)
│           ├── frontend-service.yaml      # Frontend service (port 80)
│           └── frontend-ingress.yaml      # Frontend ingress (optional)
├── carrentalbackend/
│   ├── Dockerfile                         # Enhanced multi-stage build
│   ├── .dockerignore                      # Docker build optimization
│   ├── helm/carrental/                    # Helm chart copy for reference
│   ├── DEPLOYMENT_GUIDE.md               # Deployment instructions
│   ├── pom.xml                           # Maven configuration
│   └── src/                              # Spring Boot source code
├── carrentalfrontend/
│   ├── Dockerfile                         # Multi-stage Node build
│   ├── .dockerignore                      # Docker build optimization
│   ├── helm/carrental/                    # Helm chart copy for reference
│   ├── DEPLOYMENT_GUIDE.md               # Deployment instructions
│   ├── package.json                      # Node dependencies
│   └── src/                              # React/Vite source code
├── DEPLOYMENT_GUIDE.md                   # Complete deployment guide
├── SUBMISSION_SUMMARY.md                 # Detailed submission summary
├── README.md                             # This file
└── .gitignore                            # Git ignore patterns
```

---

## 🚀 Quick Start Guide

### 1. Clone Repository
```bash
git clone https://github.com/PavanKumar-S26/Car-Rental-Managment-System.git
cd Car-Rental-Managment-System
```

### 2. Build Docker Images
```bash
# Backend
cd carrentalbackend
docker build -t carrentalbackend:1.0 .
cd ..

# Frontend
cd carrentalfrontend
docker build -t carrentalfrontend:1.0 .
cd ..
```

### 3. Push to Registry (if needed)
```bash
docker tag carrentalbackend:1.0 yourusername/carrentalbackend:1.0
docker tag carrentalfrontend:1.0 yourusername/carrentalfrontend:1.0
docker push yourusername/carrentalbackend:1.0
docker push yourusername/carrentalfrontend:1.0
```

### 4. Deploy to Kubernetes
```bash
# Create namespace
kubectl create namespace carrental

# Install Helm chart
helm install carrental ./helm/carrental \
  --namespace carrental \
  --set backend.image.repository=carrentalbackend \
  --set backend.image.tag=1.0 \
  --set frontend.image.repository=carrentalfrontend \
  --set frontend.image.tag=1.0
```

### 5. Verify Deployment
```bash
kubectl get pods -n carrental
kubectl get svc -n carrental
kubectl get configmap -n carrental
kubectl get secrets -n carrental
```

### 6. Access Services
```bash
# Frontend (on localhost:3000)
kubectl port-forward svc/frontend-service 3000:80 -n carrental

# Backend (on localhost:8080)
kubectl port-forward svc/backend-service 8080:8080 -n carrental
```

---

## 🔧 Configuration Reference

### Key values.yaml Parameters

**Frontend Configuration:**
```yaml
frontend:
  enabled: true                              # Enable/disable frontend
  replicaCount: 2                           # Number of pods
  image:
    repository: carrentalfrontend           # Docker image
    tag: latest                             # Image tag
  service:
    port: 80                                # External port
    targetPort: 3000                        # Container port
  environment:
    VITE_API_BASE_URL: http://backend:8080 # Backend API URL
```

**Backend Configuration:**
```yaml
backend:
  enabled: true                              # Enable/disable backend
  replicaCount: 2                           # Number of pods
  image:
    repository: carrentalbackend            # Docker image
    tag: latest                             # Image tag
  service:
    port: 8080                              # External port
    targetPort: 8080                        # Container port
  environment:
    SPRING_DATASOURCE_URL: jdbc:mysql://mysql:3306/carrental
    SPRING_DATASOURCE_USERNAME: carrental
    SPRING_DATASOURCE_PASSWORD: changeme
  jwt:
    secret: your-secret-key-change-me       # JWT secret
```

---

## 📊 Configurable Components

### Images
✅ Frontend image repository and tag  
✅ Backend image repository and tag  
All configurable via `values.yaml` or `--set` flags

### Ports
✅ Frontend service port (default: 80) → container port (default: 3000)  
✅ Backend service port (default: 8080) → container port (default: 8080)  
All configurable via `values.yaml`

### Replicas
✅ Frontend replicas (default: 2, configurable)  
✅ Backend replicas (default: 2, configurable)  
Enables auto-scaling and high availability

### Resources
✅ CPU limits and requests for both services  
✅ Memory limits and requests for both services  
All configurable based on cluster capacity

### Environment
✅ Database connection details  
✅ JWT secrets and configurations  
✅ API URLs and endpoints  
✅ Node environment settings

---

## 🐳 Docker Images

### Backend Image
- **Base Images**: Maven 3.9 (build) → Eclipse Temurin 21 JRE (runtime)
- **Size**: ~400MB
- **Features**:
  - Multi-stage build for optimization
  - Health checks for Kubernetes probes
  - Environment variable support
  - Ports: 8080 (primary), 8081 (secondary)

### Frontend Image
- **Base Images**: Node.js 18 (build) → Node.js 18 Alpine (runtime)
- **Size**: ~150MB
- **Features**:
  - Multi-stage build with Vite
  - Health checks for Kubernetes
  - Serve utility for SPA hosting
  - Port: 3000

---

## 📚 Documentation Files

### DEPLOYMENT_GUIDE.md
Complete guide covering:
- Step-by-step deployment instructions
- Docker image building and pushing
- Helm chart installation
- Configuration customization
- Service access methods
- Troubleshooting guide
- Resource requirements

### helm/carrental/README.md
Helm-specific documentation:
- Prerequisites and setup
- Configuration reference
- Installation methods
- Usage examples
- Health checks
- Security considerations

### SUBMISSION_SUMMARY.md
Comprehensive submission details:
- Task completion checklist
- Architecture overview
- Feature implementation list
- Quick start guide
- Deployment architecture diagram

---

## 🔐 Security Features

✅ ConfigMaps for non-sensitive configuration  
✅ Kubernetes Secrets for sensitive data (passwords, keys)  
✅ Security context preventing privilege escalation  
✅ Health checks for probe verification  
✅ Resource limits preventing resource exhaustion  
✅ RBAC-ready namespace isolation  

---

## 🎯 Helm Chart Capabilities

### Deployment Management
- ✅ Replicate services for high availability
- ✅ Health probes (liveness, readiness, startup)
- ✅ Resource management (limits & requests)
- ✅ Environment variable injection
- ✅ Configuration management with ConfigMaps
- ✅ Secret management for sensitive data

### Networking
- ✅ LoadBalancer services for external access
- ✅ Optional Ingress support for URL routing
- ✅ Service discovery within cluster
- ✅ DNS resolution between services

### Scalability
- ✅ Horizontal Pod Autoscaling support
- ✅ Configurable replicas
- ✅ Resource-aware scheduling
- ✅ Multi-environment deployments

---

## 📋 Deployment Checklist

Before deployment, ensure:
- [ ] Kubernetes cluster is running and accessible
- [ ] kubectl is configured correctly
- [ ] Helm 3+ is installed
- [ ] Docker images are built and pushed to registry
- [ ] values.yaml is customized for your environment
- [ ] Database is set up (if not in cluster)
- [ ] JWT secrets are configured
- [ ] Required namespace exists

---

## 🐛 Troubleshooting

### Pod Not Starting
```bash
kubectl describe pod <pod-name> -n carrental
kubectl logs <pod-name> -n carrental
```

### Service Not Accessible
```bash
kubectl get svc -n carrental
kubectl get endpoints -n carrental
```

### ConfigMap/Secret Issues
```bash
kubectl get configmap -n carrental
kubectl get secrets -n carrental
kubectl describe configmap backend-config -n carrental
```

### Update Configuration
```bash
# Edit values
helm upgrade carrental ./helm/carrental -n carrental

# Force pod restart
kubectl rollout restart deployment/backend -n carrental
kubectl rollout restart deployment/frontend -n carrental
```

For more detailed troubleshooting, see `DEPLOYMENT_GUIDE.md`

---

## 🔄 Helm Commands Reference

```bash
# Validate chart
helm lint ./helm/carrental

# Dry run
helm install carrental ./helm/carrental --dry-run --debug

# Template rendering
helm template carrental ./helm/carrental

# Install chart
helm install carrental ./helm/carrental -n carrental

# Upgrade chart
helm upgrade carrental ./helm/carrental -n carrental

# Rollback
helm rollback carrental 1 -n carrental

# List releases
helm list -n carrental

# Uninstall
helm uninstall carrental -n carrental

# Get values
helm get values carrental -n carrental

# Get manifest
helm get manifest carrental -n carrental
```

---

## 📊 Resource Allocation

| Component | CPU Request | CPU Limit | Memory Request | Memory Limit |
|-----------|-------------|-----------|----------------|--------------|
| Frontend  | 250m        | 500m      | 256Mi          | 512Mi        |
| Backend   | 500m        | 1000m     | 512Mi          | 1024Mi       |
| **Total** | **750m**    | **1500m** | **768Mi**      | **1536Mi**   |

---

## 🔗 GitHub Repository

**Main Repository:** https://github.com/PavanKumar-S26/Car-Rental-Managment-System

**Available Branches:**
- `master` - Root Helm chart and configuration (primary branch)
- `main` - Backend with Helm chart and Docker files
- `frontend-deployment` - Frontend with Helm chart and Docker files

---

## 📝 Files Summary

| File | Purpose | Location |
|------|---------|----------|
| Chart.yaml | Helm chart metadata | helm/carrental/ |
| values.yaml | Configuration values | helm/carrental/ |
| _helpers.tpl | Template helpers | helm/carrental/templates/ |
| backend-*.yaml | Backend resources | helm/carrental/templates/ |
| frontend-*.yaml | Frontend resources | helm/carrental/templates/ |
| Dockerfile | Backend build | carrentalbackend/ |
| Dockerfile | Frontend build | carrentalfrontend/ |
| DEPLOYMENT_GUIDE.md | Deployment instructions | Root & each repo |
| SUBMISSION_SUMMARY.md | Submission details | Root |
| README.md | This file | Root |

---

## ✨ Next Steps

1. **Review Documentation**
   - Read DEPLOYMENT_GUIDE.md for detailed instructions
   - Read helm/carrental/README.md for Helm specifics

2. **Customize Configuration**
   - Edit helm/carrental/values.yaml
   - Set proper database credentials
   - Configure JWT secrets

3. **Build Images**
   - Build backend and frontend Docker images
   - Push to your Docker registry

4. **Deploy**
   - Create Kubernetes namespace
   - Install Helm chart with custom values
   - Verify pod deployment and service access

5. **Monitor**
   - Check pod logs and status
   - Verify service connectivity
   - Monitor resource usage

---

## 📞 Support

For issues or questions:
1. Check `DEPLOYMENT_GUIDE.md` troubleshooting section
2. Check `helm/carrental/README.md` for Helm-specific help
3. Review `SUBMISSION_SUMMARY.md` for architecture details
4. Check Kubernetes logs: `kubectl logs -n carrental <pod-name>`

---

## 📅 Project Information

- **Created**: November 17, 2025
- **Chart Version**: 1.0.0
- **App Version**: 1.0
- **Kubernetes Compatibility**: 1.19+
- **Helm Compatibility**: 3+

---

## 🎓 Learning Resources

This project demonstrates:
- Helm chart best practices
- Kubernetes deployment patterns
- Multi-stage Docker builds
- ConfigMap and Secret management
- Health check implementation
- Service discovery and networking
- Environment-specific configuration

---

**Status:** ✅ **READY FOR DEPLOYMENT**

**GitHub URL for Submission:** https://github.com/PavanKumar-S26/Car-Rental-Managment-System
