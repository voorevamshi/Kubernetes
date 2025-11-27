# 📘 Kubernetes Job – README

A **Job** in Kubernetes runs **short-lived, one-time, or batch tasks**. It ensures that pods run **until they complete successfully**, and it handles retries, failures, and parallel execution.

---

## 🔹 What Is a Job?

A **Job**:

* Runs tasks that should finish (example: backups, data processing)
* Ensures pods run to **successful completion**
* Retries failed pods
* Can run pods **in parallel**
* Stops once required completions are achieved

Common use cases:

* Email sending tasks
* Cleanup scripts
* Database migrations
* Backup & restore tasks
* Processing large datasets

---

## 🔹 Example Job YAML

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sleep-job
spec:
  template:
    spec:
      containers:
      - name: myjob
        image: ubuntu
        command: ["sh", "-c", "sleep 5"]
      restartPolicy: Never

  backoffLimit: 3               # Retry pod failures 3 times
  completions: 4                # Job completes after 4 successful runs
  activeDeadlineSeconds: 50     # Fail if job exceeds 50 seconds
  ttlSecondsAfterFinished: 30   # Auto-delete job after 30 seconds
  parallelism: 2                # Run 2 pods at the same time
```

---

## 🔹 Job Key Fields Explained

| Field                         | Meaning                                   |
| ----------------------------- | ----------------------------------------- |
| `restartPolicy: Never`        | Pod never restarts after success/failure  |
| `backoffLimit: 3`             | Retry pod failures 3 times                |
| `completions: 4`              | Job completes after 4 successful pod runs |
| `parallelism: 2`              | Run 2 pods in parallel                    |
| `activeDeadlineSeconds: 50`   | Marks as failed if job exceeds 50 sec     |
| `ttlSecondsAfterFinished: 30` | Auto-delete job after 30 seconds          |

---

## 🔹 Useful Job Commands

| Command                               | Description              |
| ------------------------------------- | ------------------------ |
| `kubectl apply -f job.yaml`           | Create Job               |
| `kubectl get jobs`                    | List Jobs                |
| `kubectl describe job <name>`         | Detailed Job info        |
| `kubectl get pods -l job-name=<name>` | List pods created by Job |
| `kubectl delete job <name>`           | Delete Job               |

---

If you want, I can also create a **CronJob README**!
