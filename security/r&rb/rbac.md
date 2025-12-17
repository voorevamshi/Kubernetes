# Kubernetes RBAC Setup – Developer User

This README explains how to configure **RBAC (Role-Based Access Control)** in Kubernetes to give a **developer** user limited permissions in the `default` namespace. It includes Role creation, RoleBinding, and testing commands.

---

## 🔹 Overview

* **Role** → Defines the allowed actions (verbs) on specific resources in a namespace.
* **RoleBinding** → Assigns a Role to a user or group.
* **User** → The developer user mapped via IAM or Kubernetes.

---

## 🔹 1️⃣ Role YAML (`developer-role.yaml`)

Allows a developer to:

* Work with `pods` and `configmaps` (get, list, watch, create, update, delete, patch)
* Manage `deployments` (get, list, delete)

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: developer-role
rules:
  - apiGroups: [""]               # Core API group
    resources: ["pods", "configmaps"]
    verbs: ["get", "list", "watch", "create", "delete", "patch", "update"]
  - apiGroups: ["apps"]           # Apps API group
    resources: ["deployments"]
    verbs: ["get", "list", "delete"]
```

---

## 🔹 2️⃣ RoleBinding YAML (`developer-rb.yaml`)

Maps the **developer user** to the `developer-role` in the `default` namespace.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: developer-rb
  namespace: default
subjects:
  - kind: User
    name: developer         # IAM username mapped in aws-auth
    apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: developer-role
  apiGroup: rbac.authorization.k8s.io
```
## 🔹 3 AWS YAML (`aws-auth.yaml`)


```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: aws-auth
  namespace: kube-system
data:
  mapUsers: |
    - userarn: arn:aws:iam::095706880412:user/developer
      username: developer
      groups:
      - developer
```

> ⚠️ **Note:** Do **not** include `mapUsers` or `mapRoles` inside the RoleBinding. User mapping in EKS is handled via `aws-auth` ConfigMap.

---

## 🔹 3️⃣ Apply RBAC Configuration

```bash
kubectl apply -f developer-role.yaml
kubectl apply -f developer-rb.yaml
```

Verify:

```bash
kubectl get roles -n default
kubectl get rolebindings -n default
```

---

## 🔹 4️⃣ Test Developer Permissions

### 4.1 Switch to developer user (EKS IAM mapped)

```bash
aws eks update-kubeconfig \
  --region <your-region> \
  --name <cluster-name> \
  --profile developer
```

### 4.2 Check permissions

```bash
kubectl auth can-i create pods --as=developer -n default
kubectl auth can-i delete deployments --as=developer -n default
```

* `yes` → Allowed
* `no` → Not allowed

### 4.3 Test Pod Creation

Create a test pod YAML (`mypod.yaml`):

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: test-pod
spec:
  containers:
  - name: nginx
    image: nginx
```

Apply as developer:

```bash
kubectl apply -f mypod.yaml --as=developer -n default
```

---

## 🔹 5️⃣ Notes

* **Roles** are namespace-scoped.
* For cluster-wide permissions, use **ClusterRole** and **ClusterRoleBinding**.
* Always test using `kubectl auth can-i` before granting production access.

---

## ✅ Summary

* Role → Defines allowed actions
* RoleBinding → Grants Role to a user/group
* IAM users in EKS are mapped via `aws-auth` ConfigMap
* Test using `kubectl auth can-i`

---

This README provides a **self-contained RBAC guide** for Kubernetes developers.
