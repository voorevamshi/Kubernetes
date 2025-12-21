# Kubernetes Deployment YAML – Detailed Explanation (Tabular Form)

Below is the same Deployment explained in a clean and readable table format.

---

## 🔹 Generate Deployment YAML (Without Creating)

| Command                                                                           | Description                                        |
| --------------------------------------------------------------------------------- | -------------------------------------------------- |
| `kubectl create deploy test --image=nginx --replicas=10 --dry-run=client -o yaml` | Generates YAML for Deployment without creating it. |

---

## 🚀 Deployment YAML (Corrected Version)

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deployment1
  labels:
    app: test
spec:
  replicas: 10
  selector:
    matchLabels:
      app: test
  strategy: {}
  template:
    metadata:
      labels:
        app: test
    spec:
      containers:
      - name: nginx
        image: nginx
        resources: {}
status: {}
```

---

## 🔹 Field-by-Field Explanation

| Section      | Field        | Meaning                                             |
| ------------ | ------------ | --------------------------------------------------- |
| `apiVersion` | `apps/v1`    | API version used for Deployments.                   |
| `kind`       | `Deployment` | Tells Kubernetes that we are creating a Deployment. |

---

## 🏷 Metadata

| Field                       | Meaning                                                      |
| --------------------------- | ------------------------------------------------------------ |
| `metadata.name: test`       | Name of the Deployment.                                      |
| `metadata.labels.app: test` | Deployment is labeled with `app=test`, useful for filtering. |

---

## ⚙ Deployment Specification (`spec`)

| Field                            | Meaning                                                      |
| -------------------------------- | ------------------------------------------------------------ |
| `replicas: 10`                   | Kubernetes will maintain **10 running Pods**.                |
| `selector.matchLabels.app: test` | Deployment will manage only Pods with label `app=test`.      |
| `strategy: {}`                   | Uses default RollingUpdate strategy (zero downtime updates). |

---

## 🧬 Pod Template (`template`)

This defines how each Pod created by the Deployment looks.

### Pod Metadata

| Field              | Meaning                                                               |
| ------------------ | --------------------------------------------------------------------- |
| `labels.app: test` | Each created Pod will have label `app=test`. Must match the selector. |

### Pod Specification

| Field        | Meaning                            |
| ------------ | ---------------------------------- |
| `containers` | List of containers inside the Pod. |

---

## 📦 Container Details

| Field           | Meaning                                       |
| --------------- | --------------------------------------------- |
| `name: nginx`   | Name of the container.                        |
| `image: nginx`  | Uses `nginx` container image from Docker Hub. |
| `resources: {}` | No resource limits or requests specified.     |

---

## 📡 Status Section

| Field        | Meaning                                                                                   |
| ------------ | ----------------------------------------------------------------------------------------- |
| `status: {}` | Kubernetes fills this at runtime with Deployment progress, replica count, and Pod status. |

---

# 🧠 In Simple Words

This Deployment:

* Creates **10 Pods**, each running **Nginx**
* Ensures 10 Pods are always running (self-healing)
* Replacement or update is done **one by one with zero downtime**
* Pods are selected using label `app=test`
* Kubernetes manages scaling, restarting, and updates automatically

---

Would you like me to also include:

✔ commands to create, list, describe, and delete this Deployment
✔ add diagrams
✔ explain Deployment vs ReplicaSet vs Pod

Just tell me what you want next.
