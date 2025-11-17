# Car Rental Management System - Helm Chart

This Helm chart deploys the Car Rental Management System with both frontend and backend services.

## Prerequisites

- Kubernetes cluster (1.19+)
- Helm 3+
- Docker images for frontend and backend pushed to a registry

## Chart Structure

```
helm/carrental/
├── Chart.yaml                 # Chart metadata
├── values.yaml               # Default configuration values
└── templates/
    ├── _helpers.tpl          # Template helpers
    ├── backend-configmap.yaml      # Backend configuration
    ├── backend-deployment.yaml     # Backend deployment
    ├── backend-secret.yaml         # Backend secrets
    ├── backend-service.yaml        # Backend service
    ├── frontend-configmap.yaml     # Frontend configuration
    ├── frontend-deployment.yaml    # Frontend deployment
    └── frontend-service.yaml       # Frontend service
```

## Configuration

### Values Override

All configurable parameters are defined in `values.yaml`. You can override them:

```bash
helm install carrental ./helm/carrental \
  --set frontend.image.tag=v1.0 \
  --set backend.image.tag=v1.0 \
  --set frontend.replicaCount=3 \
  --set backend.replicaCount=2
```

### Key Configuration Options

#### Frontend
- `frontend.enabled`: Enable/disable frontend deployment (default: true)
- `frontend.replicaCount`: Number of frontend replicas (default: 2)
- `frontend.image.repository`: Docker image repository (default: carrentalfrontend)
- `frontend.image.tag`: Docker image tag (default: latest)
- `frontend.service.port`: Service port (default: 80)
- `frontend.service.targetPort`: Container port (default: 3000)
- `frontend.environment.VITE_API_BASE_URL`: API base URL

#### Backend
- `backend.enabled`: Enable/disable backend deployment (default: true)
- `backend.replicaCount`: Number of backend replicas (default: 2)
- `backend.image.repository`: Docker image repository (default: carrentalbackend)
- `backend.image.tag`: Docker image tag (default: latest)
- `backend.service.port`: Service port (default: 8080)
- `backend.service.targetPort`: Container port (default: 8080)
- `backend.environment.SPRING_DATASOURCE_URL`: Database URL
- `backend.environment.SPRING_DATASOURCE_USERNAME`: Database username
- `backend.environment.SPRING_DATASOURCE_PASSWORD`: Database password

## Installation

### 1. Build and Push Docker Images

**Backend:**
```bash
cd carrentalbackend
docker build -t <registry>/carrentalbackend:v1.0 .
docker push <registry>/carrentalbackend:v1.0
```

**Frontend:**
```bash
cd carrentalfrontend
docker build -t <registry>/carrentalfrontend:v1.0 .
docker push <registry>/carrentalfrontend:v1.0
```

### 2. Create Namespace (Optional)

```bash
kubectl create namespace carrental
```

### 3. Install Helm Chart

```bash
helm install carrental ./helm/carrental \
  --namespace carrental \
  --set backend.image.repository=<registry>/carrentalbackend \
  --set backend.image.tag=v1.0 \
  --set frontend.image.repository=<registry>/carrentalfrontend \
  --set frontend.image.tag=v1.0
```

Or using a custom values file:

```bash
helm install carrental ./helm/carrental \
  --namespace carrental \
  -f custom-values.yaml
```

## Usage Examples

### Deploy with Custom Values

Create a `custom-values.yaml`:

```yaml
frontend:
  replicaCount: 3
  image:
    repository: myregistry.azurecr.io/carrentalfrontend
    tag: v1.0
  environment:
    VITE_API_BASE_URL: http://backend-api.example.com

backend:
  replicaCount: 2
  image:
    repository: myregistry.azurecr.io/carrentalbackend
    tag: v1.0
  environment:
    SPRING_DATASOURCE_URL: jdbc:mysql://mysql-db:3306/carrental
    SPRING_DATASOURCE_USERNAME: admin
```

Then deploy:

```bash
helm install carrental ./helm/carrental \
  --namespace carrental \
  -f custom-values.yaml
```

### Update Deployment

```bash
helm upgrade carrental ./helm/carrental \
  --namespace carrental \
  --set backend.image.tag=v1.1
```

### Rollback

```bash
helm rollback carrental 1 --namespace carrental
```

### View Release Status

```bash
helm status carrental --namespace carrental
helm get values carrental --namespace carrental
helm get manifest carrental --namespace carrental
```

### Uninstall

```bash
helm uninstall carrental --namespace carrental
```

## Accessing Services

### Frontend

```bash
kubectl port-forward svc/frontend-service 3000:80 -n carrental
# Access at http://localhost:3000
```

### Backend

```bash
kubectl port-forward svc/backend-service 8080:8080 -n carrental
# Access at http://localhost:8080
```

## Troubleshooting

### Check Pod Logs

```bash
kubectl logs -n carrental deployment/frontend
kubectl logs -n carrental deployment/backend
```

### Describe Pod

```bash
kubectl describe pod -n carrental -l component=frontend
kubectl describe pod -n carrental -l component=backend
```

### Check Events

```bash
kubectl get events -n carrental
```

### Verify Resources

```bash
kubectl get pods -n carrental
kubectl get svc -n carrental
kubectl get configmap -n carrental
kubectl get secrets -n carrental
```

## Resource Requirements

### Frontend
- CPU Limit: 500m
- CPU Request: 250m
- Memory Limit: 512Mi
- Memory Request: 256Mi

### Backend
- CPU Limit: 1000m
- CPU Request: 500m
- Memory Limit: 1024Mi
- Memory Request: 512Mi

Adjust these values in `values.yaml` based on your cluster capacity.

## Health Checks

Both deployments include:
- **Liveness Probe**: Checks if the service is still running
- **Readiness Probe**: Checks if the service is ready to accept traffic
- **Startup Probe**: Gives the service time to start up

## Security Considerations

- ConfigMaps store non-sensitive environment variables
- Secrets store sensitive data (database passwords, JWT secrets)
- Services use LoadBalancer type (can be changed to ClusterIP for internal-only access)
- Security context prevents privilege escalation

## Next Steps

1. Configure your Kubernetes cluster
2. Push Docker images to your registry
3. Customize `values.yaml` for your environment
4. Deploy using Helm
5. Monitor and scale as needed

For more information, visit https://helm.sh/docs/
