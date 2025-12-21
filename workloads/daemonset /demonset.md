# 📘 DaemonSet in Kubernetes

A **DaemonSet** ensures that **one Pod runs on every Node** in the cluster. When new nodes are added, the DaemonSet automatically adds a Pod to them. When nodes are removed, Pods are removed automatically.

Common use cases include:

* Log collectors (Fluentd, Filebeat)
* Monitoring agents (Prometheus Node Exporter)
* Storage daemons (Ceph, GlusterFS)

---

## 🔹 DaemonSet Key Features

* Ensures **one pod per node**
* Automatically creates pods on **newly added nodes**
* Deletes pods from **removed nodes**
* Often used for **agent-level workloads**

---

## 🔹 Example DaemonSet YAML

```yaml\apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: appset
  labels:
    type: webapp
spec:
  selector:
    matchLabels:
      app: appset
  template:
    metadata:
      labels:
        app: appset
    spec:
      containers:
      - name: myapp
        image: nginx:latest
```

---

## 🔹 DaemonSet Commands

### ✔ Create DaemonSet

```
kubectl apply -f daemonset.yaml
```

### ✔ List DaemonSet

```
kubectl get daemonset
```

### ✔ Describe DaemonSet

```
kubectl describe daemonset appset
```

### ✔ Delete DaemonSet

```
kubectl delete daemonset appset
```

---

## 🔹 Check Which Node the DaemonSet Pods Are Running On

```
kubectl get pods -o wide
```

This shows each pod and the node it is running on.

---

## 🔹 Why Two Pods?

Two Pods appear because:

* You have **two worker nodes**
* DaemonSet creates **one pod per node**

---

If you want, I can also add **RollingUpdate strategy for DaemonSet** or **Real-world DaemonSet examp
