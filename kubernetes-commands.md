

# ⚙ Kubernetes Commands – Quick Reference

## 🔹 Pod Commands

| Command                            | Description                                      |
| ---------------------------------- | ------------------------------------------------ |
| `kubectl run webapp --image=nginx` | Creates a **Pod** similar to `docker run nginx`. |
| `kubectl get pods`                 | Lists all Pods.                                  |
| `kubectl describe pod webapp`      | Shows full details of the Pod.                   |
| `kubectl delete pod webapp`        | Deletes a Pod.                                   |
| `kubectl apply -f pod.yaml`        | Creates a Pod from YAML file.                |
---

## 🔹 Deployment Commands

| Command                                           | Description                                        |
| ------------------------------------------------- | -------------------------------------------------- |
| `kubectl create deployment webapp2 --image=nginx` | Creates a **Deployment** with ReplicaSet and Pods. |
| `kubectl get deployments`                         | Lists Deployments.                                 |
| `kubectl delete deployment webapp2`               | Deletes a Deployment.                              |
| `kubectl apply -f deployment.yaml`                | Creates Deployment from YAML.  |

---

## 🔹 Generate Deployment YAML (Without Creating)

| Command                                                                           | Description                                        |
| --------------------------------------------------------------------------------- | -------------------------------------------------- |
| `kubectl create deploy test --image=nginx --replicas=10 --dry-run=client -o yaml` | Generates YAML for Deployment without creating it. |

Save to file:

```bash
kubectl create deploy test --image=nginx --replicas=10 --dry-run=client -o yaml > deployment.yaml
```
---

## 🔹 Service (NodePort) Commands

| Command                                                         | Description                            |
| --------------------------------------------------------------- | -------------------------------------- |
| `kubectl expose pod webapp --type=NodePort --port=9009`         | Exposes a **Pod** as NodePort service. |
| `kubectl expose deployment webapp2 --type=NodePort --port=9009` | Exposes a **Deployment** as NodePort.  |
| `kubectl get svc`                                               | Lists all Services.                    |
| `kubectl delete svc webapp`                                     | Deletes a Service.                     |

---

## 🔹 Node Commands

| Command                     | Description                                |
| --------------------------- | ------------------------------------------ |
| `kubectl get nodes -o wide` | Shows nodes with internal IPs and details. |

---

## 🔹 Accessing NodePort Applications

| Method                | Command                        |
| --------------------- | ------------------------------ |
| Access from inside VM | `http://<NODE-IP>:<NODE-PORT>` |
| Example               | `http://172.30.1.2:31982`      |

---

## 🔹 Port Forwarding (Access from Laptop Browser)

| Command                                     | Description                             |
| ------------------------------------------- | --------------------------------------- |
| `kubectl port-forward svc/webapp 8080:9009` | Forwards service port to local machine. |
| Browser access                              | `http://localhost:8080`                 |

---

## 🔹 Cluster Testing (Inside VM)

| Command                        | Description                                 |
| ------------------------------ | ------------------------------------------- |
| `curl http://172.30.1.2:31982` | Tests NodePort service from inside cluster. |

---

# 💡 Difference Summary

| Component            | Meaning                                          |
| -------------------- | ------------------------------------------------ |
| **Pod**              | Runs one or more containers.                     |
| **Deployment**       | Manages Pods via ReplicaSets.                    |
| **NodePort Service** | Exposes Pods externally on `<NodeIP>:<NodePort>` |

---

This table gives a clean, interview-friendly view of all Kubernetes commands used till now.
