# NBC Bank GitOps Repository

This repository contains Kubernetes manifests for deploying the NBC Bank application using GitOps principles with ArgoCD.

## Repository Structure

```
nbc-bank-gitops/
├── README.md                    # This documentation
├── .gitignore                  # Git ignore patterns
├── argocd-application.yaml     # ArgoCD application definition
└── templates/                  # Kubernetes manifests
    ├── mysql-deployment.yaml   # MySQL database deployment
    ├── nbc-app-deployment.yaml # NBC Bank application deployment
    ├── secrets.yaml            # Kubernetes secrets
    └── services.yaml           # Services and ingress configuration
```

## Prerequisites

- Kubernetes cluster (1.20+)
- ArgoCD installed and configured
- NGINX Ingress Controller
- cert-manager (for SSL certificates)
- Storage class for persistent volumes

## Components

### 1. MySQL Database
- **Deployment**: Single replica MySQL 8.0 instance
- **Storage**: 10Gi persistent volume claim
- **Security**: Secrets-based authentication
- **Resources**: 256Mi-512Mi memory, 250m-500m CPU

### 2. NBC Bank Application
- **Deployment**: 3 replicas for high availability
- **Image**: `nbc-bank/app:latest`
- **Port**: 8080 (container), 80 (service)
- **Health Checks**: Liveness and readiness probes
- **Resources**: 128Mi-256Mi memory, 100m-200m CPU

### 3. Services
- **MySQL Service**: ClusterIP on port 3306
- **App Service**: LoadBalancer on port 80
- **Ingress**: NGINX-based with SSL termination

### 4. Secrets
- **MySQL Secrets**: Database credentials
- **App Secrets**: JWT signing keys

## Deployment

### Using ArgoCD

1. **Update Repository URL**: Modify `argocd-application.yaml` with your actual repository URL
2. **Apply ArgoCD Application**:
   ```bash
   kubectl apply -f argocd-application.yaml
   ```

### Manual Deployment

1. **Create Namespace**:
   ```bash
   kubectl create namespace nbc-bank
   ```

2. **Apply Secrets**:
   ```bash
   kubectl apply -f templates/secrets.yaml
   ```

3. **Deploy Database**:
   ```bash
   kubectl apply -f templates/mysql-deployment.yaml
   ```

4. **Deploy Application**:
   ```bash
   kubectl apply -f templates/nbc-app-deployment.yaml
   ```

5. **Apply Services**:
   ```bash
   kubectl apply -f templates/services.yaml
   ```

## Configuration

### Environment Variables

The NBC Bank application expects the following environment variables:
- `DB_HOST`: MySQL service hostname
- `DB_PORT`: MySQL service port
- `DB_NAME`: Database name
- `DB_USER`: Database username
- `DB_PASSWORD`: Database password
- `JWT_SECRET`: JWT signing secret

### Secrets Management

**Important**: Update the base64-encoded secrets in `templates/secrets.yaml` with your actual values:

```bash
# Generate base64 encoded secrets
echo -n "your-actual-password" | base64
echo -n "your-super-secret-jwt-key" | base64
```

### Domain Configuration

Update the ingress host in `templates/services.yaml`:
```yaml
hosts:
  - nbc-bank.your-domain.com  # Replace with your actual domain
```

## Monitoring and Health Checks

- **Liveness Probe**: `/health` endpoint every 10 seconds
- **Readiness Probe**: `/ready` endpoint every 5 seconds
- **Resource Limits**: Configured for both CPU and memory

## Security Considerations

- Secrets are stored as Kubernetes secrets (not in plain text)
- Database access is restricted to the application namespace
- SSL/TLS termination at the ingress level
- Resource limits prevent resource exhaustion

## Troubleshooting

### Common Issues

1. **Database Connection Failures**:
   - Verify MySQL pod is running
   - Check secret references
   - Validate network policies

2. **Application Startup Issues**:
   - Check resource availability
   - Verify secret mounting
   - Review health check endpoints

3. **Ingress Issues**:
   - Ensure NGINX ingress controller is running
   - Verify TLS certificate configuration
   - Check domain DNS resolution

### Debug Commands

```bash
# Check pod status
kubectl get pods -n nbc-bank

# View logs
kubectl logs -f deployment/nbc-app -n nbc-bank
kubectl logs -f deployment/mysql -n nbc-bank

# Check services
kubectl get svc -n nbc-bank

# Verify secrets
kubectl get secrets -n nbc-bank
```

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test the deployment
5. Submit a pull request

## License

This project is licensed under the MIT License.
