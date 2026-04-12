# Inventory API Deployment Guide (Kubernetes)

This document outlines the steps to deploy the **Inventory API** (Spring Boot) to an EKS cluster using Docker Hub and Kubernetes Manifests.

## 1. Prerequisites

-   **Docker Hub Account**: Image must be pushed to `vamshivoore/inventory-api:v1`.
    
-   **Kubectl**: Configured to point to the `ap-south-2` (Hyderabad) EKS cluster.
    
-   **AWS EKS Cluster**: Ensure nodes are running (`kubectl get nodes`).
    

----------

## 2. Handling Docker Hub Credentials

If your image is **Public**, you can skip this step. If you switch the repository to **Private**, the cluster needs a "Secret" to pull the image.

### A. Generate Access Token

1.  Log in to [Docker Hub](https://hub.docker.com/).
    
2.  Go to **Account Settings** > **Security** > **New Access Token**.
    
3.  Copy the token.
    

### B. Create Kubernetes Secret

Bash

```
kubectl create secret docker-registry my-registry-key \
    --docker-server=https://index.docker.io/v1/ \
    --docker-username=<your-username> \
    --docker-password=<your-access-token> \
    --docker-email=<your-email>

```

----------

## 3. Deployment Configuration (`deployment.yaml`)

This manifest creates 2 replicas of the application across the available nodes for high availability.

YAML

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: inventory-api-deployment
  labels:
    app: inventory-api
spec:
  replicas: 2
  selector:
    matchLabels:
      app: inventory-api
  template:
    metadata:
      labels:
        app: inventory-api
    spec:
      # Uncomment the line below if using a private repository
      # imagePullSecrets:
      # - name: my-registry-key
      containers:
      - name: inventory-api-container
        image: vamshivoore/inventory-api:v1
        ports:
        - containerPort: 8080
        imagePullPolicy: Always

```

----------

## 4. Service Configuration (`service.yaml`)

Exposes the application externally using an AWS Load Balancer.

YAML

```
apiVersion: v1
kind: Service
metadata:
  name: inventory-api-service
spec:
  selector:
    app: inventory-api
  ports:
    - protocol: TCP
      port: 80         # Public port (Browser)
      targetPort: 8080 # Spring Boot port
  type: LoadBalancer

```

----------

## 5. Execution Steps

### Step 1: Apply Manifests

Bash

```
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml

```

### Step 2: Verify Pod Distribution

To see which nodes are hosting your pods:

Bash

```
kubectl get pods -o wide

```

### Step 3: Get the External URL

Retrieve the DNS name of the AWS Load Balancer:

Bash

```
kubectl get svc inventory-api-service

```

_Wait 2-5 minutes for the DNS to propagate and the Load Balancer health checks to pass._

----------

## 6. Monitoring & Troubleshooting

### Check Application Logs

Bash

```
# View logs for a specific pod
kubectl logs <pod-name>

# Follow logs in real-time
kubectl logs -f -l app=inventory-api

```

### Scale the Application

If traffic increases, scale the pods instantly:

Bash

```
kubectl scale deployment inventory-api-deployment --replicas=5

```

### Common Issues

-   **ImagePullBackOff**: Check if the image tag `v1` exists or if the `imagePullSecrets` are missing/incorrect.
    
-   **Pending Status**: Check if the nodes have enough resources (CPU/RAM).
    
-   **Connection Timed Out**: Ensure the AWS Security Groups for your EKS nodes allow traffic on the NodePort (visible via `kubectl get svc`).
