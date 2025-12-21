# Example Pod YAML

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: webapp
  labels:
    type: frontend
spec:
  containers:
  - name: app
    image: busybox
    command: ["sh", "-c", "sleep 10"]
```


### Pod Example With Restart Policy:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: webapp
spec:
  restartPolicy: Never
  containers:
  - name: app
    image: busybox
    command: ["sh", "-c", "sleep 10"]
```

### Pod With Two Containers:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod5
spec:
  containers:
  - name: app
    image: busybox
    command: ["sh", "-c", "sleep 10"]
  - name: app2
    image: busybox
    command: ["sh", "-c", "sleep 5"]
```

---



## ▶ Explanation of Key Fields

### **command: ["sh", "-c", "sleep 10"]**

This means Kubernetes will execute the command inside the container like:

```bash
sh -c "sleep 10"
```

* `sh` → Shell
* `-c` → Execute a command string
* `sleep 10` → Run for 10 seconds and then exit

So the container **runs for 10 seconds and stops**.

---

# 🚨 Why Pod Goes to CrashLoopBackOff?

When the container exits after 10 seconds, Kubernetes thinks:

> “Container stopped unexpectedly — restart it.”

So it keeps restarting the pod again and again.

You may see output like:

This means:

| Status               | Meaning                                       |
| -------------------- | --------------------------------------------- |
| **Completed**        | Sleep finished and container exited normally  |
| **Running**          | Currently running                             |
| **CrashLoopBackOff** | Kubernetes is restarting container repeatedly |

### 🔁 Reason

Because pod keeps exiting, Kubernetes continuously restarts it.

---

# ✔ How to Fix

## 1️⃣ Keep the container alive

```yaml
command: ["sh", "-c", "tail -f /dev/null"]
```

## 2️⃣ Stop Kubernetes from restarting it

```yaml
restartPolicy: Never
```

