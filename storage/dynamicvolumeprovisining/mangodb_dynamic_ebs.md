# MongoDB StatefulSet with Dynamic EBS Provisioning (EKS)

This README explains how to deploy **MongoDB as a StatefulSet** on **Amazon EKS** using **dynamic volume provisioning** with the **AWS EBS CSI Driver**.

The setup uses:

* `app-deploy.yaml` → MongoDB StatefulSet + Headless Service
* `storage-cls.yaml` → StorageClass for dynamic EBS volume creation

---

## 📦 Files Overview

### 1️⃣ app-deploy.yaml


```yaml
# apiVersion: apps/v1
# kind: Deployment
# metadata:
#   labels:
#     app: database
#   name: database
# spec:
#   replicas: 1
#   selector:
#     matchLabels:
#       app: database
#   template:
#     metadata:
#       labels:
#         app: database
#     spec:
#       containers:
#         - image: mongo
#           name: mongotest
#           env:
#             - name: MONGO_INITDB_ROOT_USERNAME
#               value: admin
#             - name: MONGO_INITDB_ROOT_PASSWORD
#               value: testtesttest
#           volumeMounts:
#             - mountPath: /data/db
#               name: db-store
# volumes:
#   - name: db-store
#     persistentVolumeClaim:
#       claimName: mongo-data-claim
# ---
# apiVersion: v1
# kind: Service
# metadata:
#   name: mongo-svc
# spec:
#   selector:
#     app: database
#   ports:
#     - port: 27017
#       protocol: TCP
#       targetPort: 27017
#   type: NodePort

apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mongodb
spec:
  serviceName: mongodb
  replicas: 3
  selector:
    matchLabels:
      app: mongodb
  template:
    metadata:
      labels:
        app: mongodb
    spec:
      containers:
        - name: mongodb
          image: mongo
          ports:
            - containerPort: 27017
          env:
            - name: MONGO_INITDB_ROOT_USERNAME
              value: admin
            - name: MONGO_INITDB_ROOT_PASSWORD
              value: testtesttest
          volumeMounts:
            - name: mongodb-data
              mountPath: /data/db
  volumeClaimTemplates:
    - metadata:
        name: mongodb-data
      spec:
        accessModes: ["ReadWriteOnce"]
        storageClassName: ebs-storage
        resources:
          requests:
            storage: 1Gi
---
apiVersion: v1
kind: Service
metadata:
  name: mongodb
spec:
  selector:
    app: mongodb
  ports:
    - protocol: TCP
      port: 27017
      targetPort: 27017
  clusterIP: None

```

Creates:

* **StatefulSet** with 3 MongoDB replicas
* **PersistentVolumeClaims** (one per pod)
* **Headless Service** for stable DNS

Each MongoDB pod gets its **own EBS volume**.

### 2️⃣ storage-cls.yaml
```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: ebs-storage
provisioner: ebs.csi.aws.com
volumeBindingMode: WaitForFirstConsumer #Immediate
reclaimPolicy: Delete #Retain
parameters:
  type: gp2
```
Creates:

* **StorageClass** backed by AWS EBS (`gp2`)
* Enables **dynamic provisioning** of volumes

---

## ✅ Prerequisites

Before applying these YAMLs, ensure the following are ready:

### 🔹 1. AWS & EKS

* An **EKS cluster** is created and running
* Worker nodes (EC2) are in **Ready** state

```bash
kubectl get nodes
```

---

### 🔹 2. kubectl & AWS CLI

* `kubectl` installed and configured
* `aws` CLI configured

```bash
aws configure
kubectl cluster-info
```

---

### 🔹 3. IAM Role for Worker Nodes

Worker node IAM role must have:

* **AmazonEBSCSIDriverPolicy** (recommended)

OR (for learning only):

* **AdministratorAccess**

Verify role (example):

```
eksctl-demo-cluster-nodegroup-ng-NodeInstanceRole
```

---

### 🔹 4. AWS EBS CSI Driver (MANDATORY)

The EBS CSI driver **must be installed** in the cluster.

Check if installed:

```bash
kubectl get pods -n kube-system | grep ebs
```

If not installed (EKS add-on recommended):
```bash
 eksctl utils associate-iam-oidc-provider   --region ap-south-2   --cluster demo-cluster   --approve
```
```bash
 eksctl create addon   --name aws-ebs-csi-driver   --cluster demo-cluster   --region ap-south-2
```

---

## 🚀 Deployment Steps

### Step 1️⃣ Apply StorageClass

```bash
kubectl apply -f storage-cls.yaml
```

Verify:

```bash
kubectl get sc
kubectl describe sc ebs-storage
```

---

### Step 2️⃣ Apply MongoDB StatefulSet

```bash
kubectl apply -f app-deploy.yaml
```

---

## 🔍 Verification

### Check StatefulSet

```bash
kubectl get statefulset
```

### Check Pods

```bash
kubectl get pods
```

Expected:

```
mongodb-0   Running
mongodb-1   Running
mongodb-2   Running
```

---

### Check PVCs

```bash
kubectl get pvc
```

Expected:

* One PVC per pod
* Status: **Bound**

---

### Check PVs

```bash
kubectl get pv
```

Each PV corresponds to an **AWS EBS volume**.

---

## 🧠 How This Works (Important Concept)

* StatefulSet creates **1 PVC per pod**
* PVC requests storage from **StorageClass**
* EBS CSI driver creates **EBS volumes dynamically**
* Volumes are attached to EC2 nodes where pods run
* Pods get **stable identity**:

  * `mongodb-0`
  * `mongodb-1`
  * `mongodb-2`

---

## 🌐 Headless Service & DNS

Because `clusterIP: None`:

Each pod gets a stable DNS:

```
mongodb-0.mongodb.default.svc.cluster.local
mongodb-1.mongodb.default.svc.cluster.local
mongodb-2.mongodb.default.svc.cluster.local
```

Used by databases for **replication & clustering**.

---

## 🔐 Security Notes

* MongoDB is **NOT exposed publicly**
* Use **kubectl port-forward** for local access
* Do NOT use LoadBalancer for databases in production

---

## 🧪 Connect to MongoDB (Local)

```bash
kubectl port-forward pod/mongodb-0 27017:27017
```

MongoDB Compass URI:

```
mongodb://admin:testtesttest@localhost:27017
```

---

## 🧹 Cleanup

```bash
kubectl delete -f app-deploy.yaml
kubectl delete -f storage-cls.yaml
```

⚠️ With `reclaimPolicy: Delete`, EBS volumes will be deleted automatically.

---

## ✅ Key Takeaways (Interview Ready)

* StatefulSet + PVC = stable storage
* StorageClass enables dynamic provisioning
* EBS CSI driver is mandatory on EKS
* Headless Service provides stable DNS
* One EBS volume per MongoDB pod

---

✔️ You now have production-style MongoDB storage on EKS 🚀
