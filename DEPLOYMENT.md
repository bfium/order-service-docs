# CoffeeMesh Deployment Guide

## Overview

This guide covers deployment options for the CoffeeMesh application, from local development to production Kubernetes clusters.

## Prerequisites

- Docker and Docker Compose
- Kubernetes cluster (for production)
- kubectl configured
- Python 3.10+ (for local development)
- Node.js 16+ (for frontend development)

## Local Development

### Quick Start

1. **Clone and setup**:
   ```bash
   cd final
   cp env.example .env
   # Edit .env with your configuration
   ```

2. **Start services**:
   ```bash
   make up
   # or
   docker-compose up -d
   ```

3. **Run migrations**:
   ```bash
   make migrate
   # or
   cd orders && alembic upgrade head
   ```

4. **Access services**:
   - API: http://localhost:8000
   - UI: http://localhost:3000
   - API Docs: http://localhost:8000/docs

### Development Commands

```bash
# Build all images
make build

# Start development environment
make dev

# Run tests
make test

# View logs
make logs

# Stop services
make down

# Clean up
make clean
```

## Docker Deployment

### Single Service

```bash
# Build orders service
cd orders
docker build -t coffeemesh/orders-service:latest .

# Run with environment variables
docker run -p 8000:8000 \
  -e DB_URL=sqlite:///orders.db \
  -e AUTH_ON=False \
  coffeemesh/orders-service:latest
```

### Multi-Service with Docker Compose

```bash
# Production deployment
docker-compose -f docker-compose.prod.yml up -d

# Scale services
docker-compose up -d --scale orders-api=3
```

## Kubernetes Deployment

### Prerequisites

- Kubernetes cluster (v1.20+)
- kubectl configured
- Ingress controller (nginx recommended)

### Deploy to Kubernetes

1. **Create namespace**:
   ```bash
   kubectl apply -f k8s/namespace.yaml
   ```

2. **Deploy secrets**:
   ```bash
   kubectl apply -f k8s/database-secret.yaml
   ```

3. **Deploy services**:
   ```bash
   kubectl apply -f k8s/orders-service-deployment.yaml
   kubectl apply -f k8s/orders-service-service.yaml
   ```

4. **Deploy ingress**:
   ```bash
   kubectl apply -f k8s/orders-service-ingress.yaml
   ```

### Kubernetes Commands

```bash
# Deploy all services
make k8s-deploy

# Delete all services
make k8s-delete

# Check deployment status
kubectl get pods -n coffeemesh

# View logs
kubectl logs -f deployment/orders-service -n coffeemesh

# Scale deployment
kubectl scale deployment orders-service --replicas=5 -n coffeemesh
```

## Production Configuration

### Environment Variables

```bash
# Database
DB_URL=postgresql://user:pass@host:5432/db

# Authentication
AUTH_ON=True

# API Configuration
API_HOST=0.0.0.0
API_PORT=8000

# Frontend
VUE_APP_BASE_URL=https://api.coffeemesh.com
VUE_APP_AUTH0_DOMAIN=coffeemesh.eu.auth0.com
VUE_APP_AUTH0_CLIENT_ID=your-client-id
```

### Database Setup

#### PostgreSQL

1. **Create database**:
   ```sql
   CREATE DATABASE coffeemesh_orders;
   CREATE USER coffeemesh WITH PASSWORD 'secure_password';
   GRANT ALL PRIVILEGES ON DATABASE coffeemesh_orders TO coffeemesh;
   ```

2. **Run migrations**:
   ```bash
   cd orders
   alembic upgrade head
   ```

### SSL/TLS Configuration

#### Using Let's Encrypt

```yaml
# k8s/tls-secret.yaml
apiVersion: v1
kind: Secret
metadata:
  name: tls-secret
  namespace: coffeemesh
type: kubernetes.io/tls
data:
  tls.crt: <base64-encoded-cert>
  tls.key: <base64-encoded-key>
```

#### Ingress with TLS

```yaml
# k8s/orders-service-ingress-tls.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: orders-service-ingress
  namespace: coffeemesh
  annotations:
    cert-manager.io/cluster-issuer: "letsencrypt-prod"
spec:
  tls:
  - hosts:
    - orders.coffeemesh.com
    secretName: orders-tls
  rules:
  - host: orders.coffeemesh.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: orders-service
            port:
              number: 80
```

## Monitoring and Logging

### Health Checks

```bash
# Check API health
curl http://localhost:8000/docs/orders

# Kubernetes health check
kubectl get pods -n coffeemesh
```

### Logging

```bash
# Docker logs
docker-compose logs -f orders-api

# Kubernetes logs
kubectl logs -f deployment/orders-service -n coffeemesh
```

### Metrics

- Application metrics available at `/metrics`
- Kubernetes metrics via Prometheus
- Custom business metrics in application

## Scaling

### Horizontal Scaling

```bash
# Docker Compose
docker-compose up -d --scale orders-api=3

# Kubernetes
kubectl scale deployment orders-service --replicas=5 -n coffeemesh
```

### Database Scaling

- Use connection pooling
- Consider read replicas for read-heavy workloads
- Implement caching with Redis

## Backup and Recovery

### Database Backup

```bash
# PostgreSQL backup
pg_dump -h localhost -U postgres coffeemesh_orders > backup.sql

# Restore
psql -h localhost -U postgres coffeemesh_orders < backup.sql
```

### Kubernetes Backup

```bash
# Backup resources
kubectl get all -n coffeemesh -o yaml > coffeemesh-backup.yaml

# Restore
kubectl apply -f coffeemesh-backup.yaml
```

## Troubleshooting

### Common Issues

1. **Database Connection Issues**:
   - Check DB_URL environment variable
   - Verify database is running
   - Check network connectivity

2. **Authentication Issues**:
   - Verify AUTH_ON setting
   - Check JWT token validity
   - Verify Auth0 configuration

3. **Kubernetes Issues**:
   - Check pod status: `kubectl get pods -n coffeemesh`
   - View logs: `kubectl logs -f deployment/orders-service -n coffeemesh`
   - Check events: `kubectl get events -n coffeemesh`

### Debug Mode

```bash
# Enable debug logging
export LOG_LEVEL=DEBUG

# Run with debug
python orders/run.py
```

## Security Considerations

- Use secrets management for sensitive data
- Enable TLS/SSL in production
- Implement proper RBAC in Kubernetes
- Regular security updates
- Network policies for service isolation
- Input validation and sanitization
