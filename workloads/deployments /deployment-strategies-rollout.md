# Kubernetes Deployment Rollout Commands & Deployment Strategies

This README covers **all essential rollout commands** and **deployment strategies** used in Kubernetes.

---

# 📌 Deployment Strategies

## 🔹 1. **Recreate Strategy**

**What happens:**

* Kubernetes **deletes all existing Pods first**.
* Then it **creates new Pods**.
* Causes **downtime** ❌.

**When to use:**

* When new Pods **cannot run alongside** old Pods (e.g., DB schema incompatible).

**YAML Example:**

```yaml
deployment:
  spec:
    strategy:
      type: Recreate
```

---

## 🔹 2. **RollingUpdate Strategy (Default)**

**What happens:**

* Kubernetes replaces Pods **gradually**.
* No downtime ✔ (zero-downtime updates).
* It uses:

  * `maxUnavailable` → how many pods can be down
  * `maxSurge` → how many extra pods can be created temporarily

**YAML Example:**

```yaml
deployment:
  spec:
    strategy:
      type: RollingUpdate
      rollingUpdate:
        maxUnavailable: 1
        maxSurge: 1
```

**Recreate Example:**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myappone
  labels:
    type: webapp

spec:
  replicas: 10
  selector:
    matchLabels:
      type: webapp

  strategy:
    type: Recreate     # Deployment strategy

  template:
    metadata:
      name: myapp-pod
      labels:
        type: webapp

    spec:
      containers:
      - name: mywebapp
        image: nginx:latest

```

---

## 🔹 Update Deployment Image (creates new revision)

```bash
kubectl set image deployment <name> <container>=<new-image>
```

Example:

```bash
kubectl set image deployment myapp nginx=nginx:1.25
```

---

## 🔹 Edit Deployment YAML (also creates new revision)

```bash
kubectl edit deployment <name>
```

Saving changes automatically triggers a new rollout.

---

## 🔹 Apply Deployment YAML

```bash
kubectl apply -f deploy.yaml
```

If spec changes → Kubernetes creates a new revision.

---

# 📌 Rollout Commands

| Command                                                      | Description                           |
| ------------------------------------------------------------ | ------------------------------------- |
| `kubectl rollout status deployment <name>`                   | Shows rollout progress.               |
| `kubectl rollout history deployment <name>`                  | Shows revisions history.              |
| `kubectl rollout history deployment <name> --revision=<num>` | Shows details of a specific revision. |
| `kubectl rollout undo deployment <name>`                     | Rollback to previous revision.        |
| `kubectl rollout undo deployment <name> --to-revision=<num>` | Rollback to specific revision.        |
| `kubectl set image deployment <name> <container>=<image>`    | Updates image → new rollout.          |
| `kubectl edit deployment <name>`                             | Edit and trigger new rollout.         |
| `kubectl apply -f deploy.yaml`                               | Apply changes → new rollout.          |

---

If you want, I can also add ** diagrams, flowcharts, examples, or interview explanati
