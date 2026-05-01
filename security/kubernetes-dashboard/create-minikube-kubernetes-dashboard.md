# Kubernetes Dashboard Setup Guide (Manual Installation)

This guide provides a step-by-step process to deploy and access the Kubernetes Dashboard on an EKS cluster without using Helm.

## Prerequisites

-   `kubectl` installed and configured.
    
-   Access to the cluster with admin permissions.
    

----------

## Step 1: Create the Namespace

The dashboard components and security manifests live in a dedicated namespace.

Bash

```
kubectl create namespace kubernetes-dashboard

```

## Step 2: Deploy Kubernetes Dashboard

Apply the official recommended manifests from the Kubernetes Dashboard GitHub repository.

Bash

```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml

```

## Step 3: Configure Admin Access

To log in, we need a **ServiceAccount** with cluster-wide administrative privileges and a persistent **Secret** to store the login token.

1.  **Create the Admin User & Role Binding:** Apply your `admin-user.yaml` to create the account.
    
    Bash
    
    ```
    kubectl apply -f admin-user.yaml
    
    ```
    
2.  **Create the Long-Lived Token Secret:** Apply your `sa-secret-admin.yaml` to link a static token to the account.
    
    Bash
    
    ```
    kubectl apply -f sa-secret-admin.yaml
    
    ```
    

## Step 4: Retrieve the Login Token

Use the following command to extract the token needed for the Dashboard login screen:

Bash

```
kubectl -n kubernetes-dashboard get secret admin-sa-secret -o jsonpath={".data.token"} | base64 -d

```

> **Note:** Copy the entire output string carefully.

## Step 5: Access the Dashboard

Because the dashboard is not exposed to the public internet by default, we use a local proxy.

1.  **Start the Proxy:**
    
    Bash
    
    ```
    kubectl proxy
    
    ```
    
    _Keep this terminal window open._
    
2.  **Open in Browser:** Navigate to the following URL: [http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/](https://www.google.com/search?q=http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/&authuser=3)
    
3.  **Login:**
    
    -   Select the **Token** option.
        
    -   Paste the token retrieved in Step 4.
        
    -   Click **Sign in**.
        

----------

## Troubleshooting Commands

Use these to verify your setup if something isn't working:

Bash

```
# Check if Dashboard pods are running
kubectl get pods -n kubernetes-dashboard

# Verify the ServiceAccount exists
kubectl get serviceaccount admin-user -n kubernetes-dashboard

# Check the created Secrets
kubectl get secret -n kubernetes-dashboard

```

----------

### Manifest Examples used in this guide:

**admin-user.yaml**

YAML

```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard

```

**sa-secret-admin.yaml**

YAML

```
apiVersion: v1
kind: Secret
metadata:
  name: admin-sa-secret
  namespace: kubernetes-dashboard
  annotations:
    kubernetes.io/service-account.name: admin-user
type: kubernetes.io/service-account-token

```
