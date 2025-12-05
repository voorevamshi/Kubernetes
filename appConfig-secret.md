# 📘 Kubernetes ConfigMap & Secret – Complete Guide

---

## ✅ What is a ConfigMap?

A **ConfigMap** is used to store **non-sensitive configuration data** like:

* Application usernames
* Hostnames
* URLs
* Ports
* Feature flags

It allows you to **separate configuration from application code**.

---

## ✅ What is a Secret?

A **Secret** is used to store **sensitive data** such as:

* Passwords
* API tokens
* SSH keys
* Certificates

Secrets are stored in **base64-encoded format** and protected using **RBAC**.

---

## ✅ Key Differences

| Feature  | ConfigMap          | Secret            |
| -------- | ------------------ | ----------------- |
| Stores   | Non-sensitive data | Sensitive data    |
| Encoding | Plain text         | Base64 encoded    |
| Security | Normal             | Access controlled |
| Examples | Username, URL      | Password, Token   |

---

## ✅ Example ConfigMap
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql
  labels:
    app: mysql
spec:
  selector:
    matchLabels:
      app: mysql
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
        - name: mydb
          image: mysql
          env:
            - name: MYSQL_ROOT_USER
              valueFrom:
                configMapKeyRef:
                  name: app-config
                  key: mysqlusername
            - name: MYSQL_ROOT_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: app-secret
                  key: password
          ports:
            - containerPort: 3306
#          volumeMounts:
#            - name: mysql-volume
#              mountPath: /var/lib/mysql
#      volumes:
#        - name: mysql-volume
#          persistentVolumeClaim:
#            claimName: mysql-pvc
```
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  mysqlendpoint: mysql
  mysqlusername: admin
```

### ▶ Create ConfigMap

```bash
kubectl apply -f configmap.yaml
```

---

## ✅ Example Secret

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: app-secret
data:
  password: bXlzcWxteXNxbA==
```

### ▶ Encode Password

```bash
echo "mysqlysql" | base64
```

### ▶ Create Secret

```bash
kubectl apply -f secret.yaml
```

---

## ✅ Using ConfigMap & Secret in Deployment

```yaml
env:
  - name: MYSQL_ROOT_USER
    valueFrom:
      configMapKeyRef:
        name: app-config
        key: mysqlusername

  - name: MYSQL_ROOT_PASSWORD
    valueFrom:
      secretKeyRef:
        name: app-secret
        key: password
```

---

## ✅ Runtime Flow

```
ConfigMap  → MYSQL_ROOT_USER     → admin
Secret     → MYSQL_ROOT_PASSWORD → mysqlysql
```

---

## ✅ Verify Inside Pod

```bash
kubectl exec -it <pod-name> -- env | grep MYSQL
```

Expected Output:

```
MYSQL_ROOT_USER=admin
MYSQL_ROOT_PASSWORD=mysqlysql
```

---

## ✅ CLI Commands

| Command                                 | Description        |
| --------------------------------------- | ------------------ |
| `kubectl get configmap`                 | List ConfigMaps    |
| `kubectl describe configmap app-config` | Detailed ConfigMap |
| `kubectl get secrets`                   | List Secrets       |
| `kubectl describe secret app-secret`    | Secret details     |

---

## ✅ Important Notes

* Secrets are **not encrypted by default**, only encoded
* Use **etcd encryption at rest** in production
* Never store secrets directly in GitHub

---

## ✅ Interview One-Liners

* **ConfigMap:** Used to store non-sensitive configuration data.
* **Secret:** Used to securely store sensitive information.
* **Best Practice:** Use ConfigMap for config and Secret for credentials.


