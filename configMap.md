# Kubernetes ConfigMap – README

## 📌 What is a ConfigMap?

A **ConfigMap** in Kubernetes is used to store **non-sensitive configuration data** in key–value format. It helps you keep configuration separate from application code.

✅ Used for:

* Application properties
* Environment variables
* Command arguments
* Configuration files

❌ Not for secrets (use **Secret** instead)

---

## ✅ Why Use ConfigMap?

* Externalize configuration
* Change configs without rebuilding images
* Reuse same container in different environments
* Better application portability

---

## 🏗️ Types of ConfigMap Creation

### 1️⃣ From Literal Key-Value

```bash
kubectl create configmap app-config \
  --from-literal=app.name=payment-service \
  --from-literal=app.env=prod
```

---

### 2️⃣ From a File

```bash
kubectl create configmap app-config \
  --from-file=application.properties
```

---

### 3️⃣ From a Directory

```bash
kubectl create configmap app-config --from-file=./config
```

---

### 4️⃣ Using YAML (Recommended)

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  app.name: payment-service
  app.env: prod
  app.port: "8080"
```

Apply:

```bash
kubectl apply -f configmap.yaml
```

---

## 📥 How to Use ConfigMap in a Pod

### ✅ 1. As Environment Variables

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: cm-env-pod
spec:
  containers:
    - name: app
      image: nginx
      env:
        - name: APP_NAME
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: app.name
```

---

### ✅ 2. As All Environment Variables

```yaml
envFrom:
  - configMapRef:
      name: app-config
```

---

### ✅ 3. As a Volume (File Mount)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: cm-volume-pod
spec:
  containers:
    - name: app
      image: nginx
      volumeMounts:
        - name: config-volume
          mountPath: /etc/config
  volumes:
    - name: config-volume
      configMap:
        name: app-config
```

---

## 🔍 Useful Commands

```bash
kubectl get configmaps
kubectl describe configmap app-config
kubectl delete configmap app-config
```

---

## ⚠️ Important Notes

* ConfigMaps store **plain text data**
* Max size: **1MB per ConfigMap**
* Updates do NOT auto-restart pods
* Mounted files update automatically (after some time)

---

## 🔐 ConfigMap vs Secret

| ConfigMap            | Secret                  |
| -------------------- | ----------------------- |
| Non-sensitive data   | Sensitive data          |
| Stored in plain text | Base64 encoded          |
| Configs              | Passwords, tokens, keys |

---

## ✅ Real-Time Use Case

In a **Spring Boot microservices project**, ConfigMaps are used to store:

* application.properties
* service URLs
* feature flags

Each environment (dev, test, prod) has **separate ConfigMaps**.

---

## 🧩 MySQL ConfigMap with Deployment – Example

This section explains the following **real-time MySQL ConfigMap and Deployment YAMLs** used to externalize database configuration and mount it into a MySQL container.

---

## ✅ ConfigMap: mysql-conf

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: mysql-conf
data:
  my.cnf: |
    [mysqld]
    host-cache-size=0
    skip-name-resolve
    datadir=/var/lib/sql
    socket=/var/run/mysqld/mysqld.sock
    secure-file-priv=/var/lib/mysql-files
    user=mysql
    pid-file=/var/run/mysqld/mysqld.pid

    [client]
    socket=/var/run/mysqld/mysqld.sock

    !includedir /etc/mysql/conf.d/

  ym.cnf: |
    test
```

### 🔍 What This ConfigMap Does

* Stores **MySQL server configuration** externally
* `my.cnf` → Actual MySQL configuration file
* `ym.cnf` → Sample additional config file
* These files will be **mounted inside the MySQL container**
* Helps avoid rebuilding the image for config changes

---

## ✅ Deployment: mydb

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mydb
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mydb
  template:
    metadata:
      labels:
        app: mydb
    spec:
      containers:
        - image: mysql
          name: mysql
          env:
            - name: MYSQL_ROOT_PASSWORD
              value: test
          volumeMounts:
            - name: mysql-vol
              mountPath: /etc/my.cnf
              subPath: my.cnf
#            - name: mysql-vol
#              mountPath: /ym.cnf
#              subPath: ym.cnf
      volumes:
        - name: mysql-vol
          configMap:
            name: mysql-conf
```

---

## 🔗 How ConfigMap Is Used in This Deployment

| Component             | Purpose                            |
| --------------------- | ---------------------------------- |
| `mysql-conf`          | Stores MySQL configuration files   |
| `mysql-vol`           | Volume created from ConfigMap      |
| `subPath: my.cnf`     | Mounts only `my.cnf` file          |
| `/etc/my.cnf`         | MySQL reads config from this path  |
| `MYSQL_ROOT_PASSWORD` | Root password set via env variable |

---

## 📂 Result Inside the Container

After the pod starts:

* The file `/etc/my.cnf` will contain content from **ConfigMap → my.cnf**
* MySQL will run using this external configuration

You can verify using:

```bash
kubectl exec -it <pod-name> -- cat /etc/my.cnf
```

---

## ⚠️ Important Notes

* `subPath` is used to mount **only one file** from ConfigMap
* If multiple files are needed, mount the **full directory instead**
* ConfigMap update will NOT restart the MySQL pod automatically
* This setup is ideal for **DB configuration management in Kubernetes**

---

## ✅ Real-Time Use Case

This pattern is used in:

* Database deployments (MySQL, PostgreSQL)
* Externalizing DB tuning parameters
* Environment-specific DB configs (dev/test/prod)

---

## 🎯 Interview Answer (Short)

> In this setup, a ConfigMap is used to store MySQL configuration files and mounted into the MySQL container using volumes and subPath. This allows changing database configurations without rebuilding the Docker image.


