# Kubernetes Demo App

A simple web application with MongoDB backend deployed on Kubernetes using Minikube.

## Prerequisites

- Minikube installed
- kubectl installed
- Docker installed (for Minikube)

## Architecture

- **Web Application**: Node.js app running on port 3000
- **MongoDB**: Database backend
- **ConfigMap**: Stores MongoDB connection URL
- **Secret**: Stores MongoDB credentials

## Getting Started

### 1. Start Minikube

```bash
# Start Minikube cluster
minikube start

# Verify Minikube is running
minikube status

# Get Minikube IP (needed to access the app)
minikube ip
```

### 2. Deploy the Application

Deploy components in the following order:

```bash
# 1. Create the Secret (MongoDB credentials)
kubectl apply -f mongo-secret.yaml

# 2. Create the ConfigMap (MongoDB URL)
kubectl apply -f mongo-config.yaml

# 3. Deploy MongoDB and its Service
kubectl apply -f mongo.yaml

# 4. Deploy Web Application and its Service
kubectl apply -f webapp.yaml
```

### 3. Verify Deployment

```bash
# Check all pods are running
kubectl get pods

# Check all services
kubectl get services

# Get detailed information about pods
kubectl describe pods

# Check deployments
kubectl get deployments

# View logs of a specific pod
kubectl logs <pod-name>

# Example: View webapp logs
kubectl logs -l app=webapp
```

### 4. Access the Application

The web application is exposed via NodePort service on port 30100.

```bash
# Get the Minikube IP
minikube ip

# Access the application in your browser
# URL: http://<minikube-ip>:30100
# Example: http://192.168.49.2:30100
```

Alternatively, use Minikube service command:

```bash
# This will open the webapp in your default browser
minikube service webapp-service
```

## Useful Commands

### Monitoring and Debugging

```bash
# Watch pods in real-time
kubectl get pods -w

# Get pod events
kubectl get events --sort-by='.lastTimestamp'

# Execute commands inside a pod
kubectl exec -it <pod-name> -- /bin/bash

# Check service endpoints
kubectl get endpoints

# Describe a service
kubectl describe service webapp-service
```

### Restarting Deployments

If you make changes to the YAML files:

```bash
# Apply changes
kubectl apply -f <yaml-file>

# Force restart a deployment
kubectl rollout restart deployment webapp-deployment
kubectl rollout restart deployment mongo-deployment

# Check rollout status
kubectl rollout status deployment webapp-deployment
```

### Cleanup

```bash
# Delete all resources
kubectl delete -f webapp.yaml
kubectl delete -f mongo.yaml
kubectl delete -f mongo-config.yaml
kubectl delete -f mongo-secret.yaml

# Or delete everything at once
kubectl delete -f .

# Stop Minikube
minikube stop

# Delete Minikube cluster (removes all data)
minikube delete
```

## Troubleshooting

### Application Not Accessible

1. Verify the service selector matches pod labels:
   ```bash
   kubectl get pods --show-labels
   kubectl get service webapp-service -o yaml
   ```

2. Check if pods are running:
   ```bash
   kubectl get pods
   ```

3. Check service endpoints:
   ```bash
   kubectl get endpoints webapp-service
   ```

4. Test connectivity from within the cluster:
   ```bash
   kubectl run test-pod --image=busybox -it --rm -- wget -O- webapp-service:3000
   ```

### Pod Issues

```bash
# Check pod logs
kubectl logs <pod-name>

# Check previous pod logs (if pod restarted)
kubectl logs <pod-name> --previous

# Describe pod for events and status
kubectl describe pod <pod-name>
```

### Using Minikube Dashboard

```bash
# Open Kubernetes dashboard
minikube dashboard
```

## File Structure

```
.
├── README.md           # This file
├── mongo-secret.yaml   # MongoDB credentials (base64 encoded)
├── mongo-config.yaml   # MongoDB service URL configuration
├── mongo.yaml         # MongoDB deployment and service
└── webapp.yaml        # Web application deployment and service
```

## Notes

- The web application is accessible on NodePort 30100
- MongoDB runs internally on port 27017
- Make sure the Minikube VM has enough resources (CPU/Memory)
- Default credentials are stored in mongo-secret.yaml (base64 encoded)