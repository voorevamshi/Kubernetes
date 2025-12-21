# 📘 Deployment + NodePort Service (Killercoda)

This explains how to deploy an **NGINX application using a Kubernetes Deployment** and expose it using a **NodePort Service** specifically in the **Killercoda environment**.

---

## ✅ 1. Deployment YAML

Creates **3 replicas** of an NGINX pod.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
  labels:
    type: webapp
    app: test
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

---

## ✅ 2. NodePort Service YAML

Exposes the Deployment externally using **NodePort**.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mysv-np
spec:
  type: NodePort
  selector:
    type: webapp
  ports:
    - port: 80
      targetPort: 80
```

---

## ✅ 3. Apply the YAML Files

```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

---

## ✅ 4. Verify Deployment & Service

```bash
kubectl get deployments
kubectl get pods
kubectl get svc
```

---

## ✅ 5. Get NodePort & Node IP

```bash
kubectl describe svc mysv-np
kubectl get nodes -o wide
```

Example Output:

```
NodePort: 32000
Node IP: 172.30.2.2
```

---

## ✅ 6. Access the Application (Killercoda)

### 🔹 Using CURL (Recommended in Killercoda)

```bash
curl http://172.30.2.2:32000
```

✅ This is the **correct way to test NodePort in Killercoda** since browser access is often restricted.

---

## ⚠️ Important Killercoda Notes

* Killercoda uses **internal IPs only**.
* **Direct browser access may NOT work**.
* Always verify using:

  * `curl`
  * `kubectl port-forward`

Alternative (Port Forwarding):

```bash
kubectl port-forward svc/mysv-np 8080:80
curl http://localhost:8080
```

---

## ✅ 7. Difference: ClusterIP vs NodePort (Quick)

| Feature            | ClusterIP | NodePort    |
| ------------------ | --------- | ----------- |
| External Access    | ❌ No      | ✅ Yes       |
| Killercoda Browser | ❌ No      | ⚠️ Limited  |
| Curl from Terminal | ✅ Yes     | ✅ Yes       |
| Port Range         | Internal  | 30000–32767 |

---

## ✅ 8. Cleanup

```bash
kubectl delete deployment myapp
kubectl delete service mysv-np
```

---

## ✅ Interview Summary

> A **Deployment** manages replica pods, and a **NodePort Service** exposes the application externally using a fixed port on all nodes. In **Killercoda**, NodePort is tested using `curl` rather than a browser.

---

If you want, I can also add:
✅ LoadBalancer Service README
✅ Ingress with NGINX Controller
✅ Production-level Service exposure examples
