
# 📘 Kubernetes LoadBalancer Service – Complete Guide (Killercoda + Cloud)

<img width="1280" height="575" alt="image" src="https://github.com/user-attachments/assets/ec24081c-e0b2-4e27-9afd-3f12db08f647" />

A **LoadBalancer Service** exposes your Kubernetes application to the **outside world using a cloud provider load balancer** (AWS, Azure, GCP). It automatically provisions an external IP and forwards traffic to your Pods.

> ⚠️ In **Killercoda / local clusters**, `type: LoadBalancer` does **NOT** create a real external IP because there is **no cloud provider integration**. It behaves like a **NodePort internally**.

---

## 🔹 What LoadBalancer Does

```
Internet → Cloud Load Balancer → NodePort → Pods
```

* Creates a **cloud load balancer** automatically
* Forwards traffic to **NodePort** created behind the scenes
* Provides a **public external IP** (in cloud)
* Distributes traffic across **all Pods**

---

## 🔹 Example Deployment + LoadBalancer Service

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
      labels:
        type: webapp
    spec:
      containers:
        - name: mywebapp
          image: nginx
---
apiVersion: v1
kind: Service
metadata:
  name: mysv-lb
spec:
  type: LoadBalancer
  selector:
    type: webapp
  ports:
    - port: 80
      targetPort: 80
```

---

## 🔹 Create LoadBalancer Service

```
kubectl apply -f loadbalancer.yaml
```

---

## 🔹 Check LoadBalancer Status

```
kubectl get svc mysv-lb
```

### 🔹 Output in Cloud (AWS/GCP/Azure)

```
EXTERNAL-IP   52.xx.xx.xx
```

Access using:

```
http://52.xx.xx.xx
```

### 🔹 Output in Killercoda / Minikube

```
EXTERNAL-IP   <pending>
```

This means:

* No real cloud load balancer exists
* Service works **only via NodePort internally**

---

## 🔹 How to Access LoadBalancer in Killercoda

1️⃣ Get NodePort:

```
kubectl describe svc mysv-lb
```

You will see something like:

```
NodePort: 32000
```

2️⃣ Get Node IP:

```
kubectl get nodes -o wide
```

3️⃣ Access using Node IP + NodePort:

```
http://172.30.1.2:32000
```

✔ Works inside Killercoda only
❌ Will NOT work from your laptop browser

---

## 🔹 ExternalTrafficPolicy Explained

```
ExternalTrafficPolicy: Cluster
```

* Traffic can enter **any node**
* Routed to **any Pod on any node**
* Source IP is **NOT preserved**

```
ExternalTrafficPolicy: Local
```

* Traffic goes only to **local Pods on that node**
* Source IP **IS preserved**
* If no Pod on the node → request fails

---

## 🔹 InternalTrafficPolicy Explained

```
InternalTrafficPolicy: Cluster
```

* Pod → Service → Any Pod in cluster

```
InternalTrafficPolicy: Local
```

* Pod → Service → Only same-node Pods

---

## 🔹 LoadBalancer vs NodePort vs ClusterIP

| Type         | Accessible From     | External IP | Use Case                             |
| ------------ | ------------------- | ----------- | ------------------------------------ |
| ClusterIP    | Inside Cluster Only | ❌           | Microservices internal communication |
| NodePort     | Node IP + Port      | ❌ (manual)  | Testing, labs, Killercoda            |
| LoadBalancer | Public Internet     | ✅           | Production workloads                 |

---

## 🔹 When to Use LoadBalancer

✅ Cloud production environments
✅ Public APIs
✅ Websites
✅ Mobile backends

❌ Not useful in:

* Killercoda
* Minikube (without tunnel)
* Bare metal (without MetalLB)

---

## 🔹 Important Interview Points

* LoadBalancer **creates a NodePort automatically**
* Needs **cloud provider integration**
* Uses **kube-proxy + cloud LB**
* Supports **health checks & auto-scaling**
* Preserves source IP only with `ExternalTrafficPolicy: Local`

---

✅ This README is fully aligned with **Killercoda + real cloud clusters**.

If you want, I can also add:

* ✅ MetalLB LoadBalancer for on-prem
* ✅ Minikube LoadBalancer using `minikube tunnel`
* ✅ Architecture diagrams for interviews
