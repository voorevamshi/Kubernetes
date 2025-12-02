# 🚀 Kubernetes Deployment + Service (ClusterIP) Guide

This README explains how the **Deployment** and **ClusterIP Service** work together in Kubernetes.

---

## 📌 Deployment Overview

A **Deployment** ensures your application runs in multiple identical Pods and provides rolling updates.

### ✅ Deployment YAML

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
  labels:
    type: webapp
spec:
  replicas: 3
  selector:
    matchLabels:
      type: webapp
  template:
    metadata:
      name: myapp-pod
      labels:
        type: webapp
    spec:
      containers:
      - name: mywebapp
        image: nginx
```

### 🔹 What this Deployment does

* Creates **3 Pods**
* Each Pod runs: `nginx`
* Each Pod has label: `type=webapp`
* Deployment manages updates, scaling, and restarts

---

## 📌 Service Overview (ClusterIP)

A **ClusterIP Service** exposes your Pods **inside** the cluster only.

### ✅ Service YAML

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mysv-cip
spec:
  ports:
    - port: 8081
      targetPort: 80
  selector:
    type: webapp
```

### 🔹 What this Service does

* Creates a stable internal IP
* Exposes the app on **port 8081** internally
* Forwards traffic to Pod's container **port 80** (nginx default)
* Load‑balances across all Pods with `type=webapp`

---

## 🔁 Traffic Flow

```
Client inside cluster
        ↓
Service (mysv-cip:8081)
        ↓
Pod → nginx:80
Pod → nginx:80
Pod → nginx:80
```

Service load‑balances the requests across all 3 Pods.

---

## 🧪 Test the Service (ClusterIP)

ClusterIP is internal-only, so test from inside the cluster:

### Step 1: Create test Pod

```
kubectl run test --image=busybox -it --rm -- sh
```

### Step 2: Inside pod, run:

```
wget -qO- http://mysv-cip:8081
```

You should see the **Nginx welcome page HTML**.

---

## 🧹 Useful Commands

### ✔ Apply

```
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

### ✔ Check pods

```
kubectl get pods
kubectl get pods -o wide
```

### ✔ Check service

```
kubectl get svc
```

### ✔ Describe resources

```
kubectl describe deployment myapp
kubectl describe svc mysv-cip
```

---
