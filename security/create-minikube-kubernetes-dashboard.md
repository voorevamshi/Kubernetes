## Kubernetes Local Development with Minikube

### Minikube Setup

### Prerequisites

-   **Platform:** Windows (x86-64)
    
-   **Hypervisor:** Docker Desktop, Hyper-V, or VirtualBox.
    

### Download & Installation & Execution( via PowerShell)

 **Download:** https://minikube.sigs.k8s.io/docs/start/?arch=%2Fwindows%2Fx86-64%2Fstable%2F.exe+download

Run the following commands in a PowerShell terminal as **Administrator**:

1.  **Create Directory:**
    
    PowerShell
    
    ```
    New-Item -Path 'C:\' -Name 'minikube' -ItemType Directory -Force
    
    ```
    
2.  **Download Binary:** _Ensure you have at least 100MB of free disk space on your C: drive._
    
    PowerShell
    ```
    Invoke-WebRequest -OutFile 'C:\minikube\minikube.exe' -Uri 'https://github.com/kubernetes/minikube/releases/latest/download/minikube-windows-amd64.exe' -UseBasicParsing    
    ```
    
3.  **Add to System PATH:** To run `minikube` from any terminal, add `C:\minikube` to your Environment Variables.
  -   Right-click your **PowerShell** icon.
  -   Select **"Run as Administrator"**.
 
```powershell
$oldPath = [Environment]::GetEnvironmentVariable('Path', [EnvironmentVariableTarget]::Machine)
if ($oldPath.Split(';') -inotcontains 'C:\minikube') {
    [Environment]::SetEnvironmentVariable('Path', $('{0};C:\minikube' -f $oldPath), [EnvironmentVariableTarget]::Machine)
    Write-Host "Successfully added C:\minikube to the Machine Path." -ForegroundColor Green
} else {
    Write-Host "C:\minikube is already in the Path." -ForegroundColor Yellow
}
```
	
### Starting Minikube

Restart your terminal and run:

```
minikube start
```
----------

This guide documents the initialization of a local Kubernetes cluster and the deployment of a Java-based Inventory API.

### 🚀 Cluster Initialization

### Start Minikube

To start the cluster using Docker Desktop as the engine:

```
minikube start --driver=docker
```

### Verify Cluster Health

Once started, verify that the control plane and nodes are ready:

```
# Check cluster status
kubectl cluster-info

# Check node status
kubectl get node

```

----------

### 📦 Deployment Workflow

#### 1. Naming Conventions

Kubernetes requires resource names to follow **RFC 1123** (lowercase alphanumeric characters or '-').

-   **Invalid:** `Inventory` (Uppercase not allowed)
    
-   **Valid:** `inventory` or `inventory-api`
    

#### 2. Deploying the API

We deployed the Inventory API using a Docker Hub image.

 **Note:** Always use the full image name (`username/image:tag`) to ensure the cluster can pull it correctly from the registry.

```
# Create the deployment
kubectl create deployment inventory-api --image=vamshivoore/inventory-api:v1 --port=8080

# Verify the pod is running
kubectl get pods

```

#### 3. Exposing the Service

To make the API accessible from your Windows host, we expose it as a `NodePort`.

```
# Expose the deployment
kubectl expose deployment inventory-api --type=NodePort

# Get the service details and port mapping
kubectl get service

```

----------

### 🌐 Accessing the Application

#### Tunneling with Docker Driver

Since we are using the Docker driver on Windows, the internal IP is not directly accessible. Use the minikube tunnel command to generate a local URL:

```
minikube service inventory-api --url
```

_Keep this terminal window open to maintain the connection._

#### Dashboard

To visualize the cluster and your deployments in a web interface:


```
minikube dashboard

```
**Local Minikube Dashboard url :** http://127.0.0.1:51816/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/#/workloads?namespace=default

----------

## 🛠 Useful Troubleshooting Commands

| Command | Purpose |
| :--- | :--- |
| `kubectl logs <pod-name>` | View application console output (logs) for debugging. |
| `kubectl describe pod <pod-name>` | Diagnose pod failures (e.g., ImagePullBackOff). |
| `minikube docker-env` | Configure the terminal to use Minikube's internal Docker daemon. |
| `kubectl delete deployment <name>` | Remove a deployment to perform a clean restart. |
