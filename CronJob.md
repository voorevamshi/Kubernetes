# 📘 Kubernetes CronJob – README

A **CronJob** in Kubernetes runs **Jobs on a scheduled time** (similar to Linux cron jobs). It creates Jobs at specified intervals and can handle concurrency, retries, and completion policies.

---

## 🔹 What Is a CronJob?

A **CronJob**:

* Schedules a Job at specific time intervals
* Ensures Jobs run to completion
* Can handle concurrency and failures
* Useful for recurring tasks like backups, reporting, or batch processing

Common use cases:

* Database backups
* Cleanup scripts
* Email notifications
* Periodic data processing

---

## 🔹 Example CronJob YAML

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: test
spec:
  schedule: "* * * * *"        # Runs every minute
  concurrencyPolicy: Forbid      # Do not run new job if previous is still running
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: test
            image: busybox:1.28
            command:
            - /bin/sh
            - -c
            - sleep 10
          restartPolicy: OnFailure
```

---

## 🔹 Key Fields Explained

| Field                       | Meaning                                                                             |
| --------------------------- | ----------------------------------------------------------------------------------- |
| `schedule: "* * * * *"`     | Cron expression for when the job runs (here: every minute)                          |
| `concurrencyPolicy: Forbid` | Prevents a new job if the previous is still running (other options: Allow, Replace) |
| `jobTemplate`               | Defines the Job that the CronJob will create                                        |
| `restartPolicy: OnFailure`  | Pod will restart only if it fails                                                   |

---

## 🔹 CronJob Commands

| Command                                   | Description                  |
| ----------------------------------------- | ---------------------------- |
| `kubectl apply -f cronjob.yaml`           | Create CronJob               |
| `kubectl get cronjobs`                    | List all CronJobs            |
| `kubectl describe cronjob <name>`         | Detailed CronJob info        |
| `kubectl get jobs`                        | List Jobs created by CronJob |
| `kubectl get pods -l job-name=<job-name>` | List pods for a specific job |
| `kubectl delete cronjob <name>`           | Delete CronJob               |

---

This CronJob YAML runs a **busybox container** that sleeps for 10 seconds every minute, and **does not allow concurrent execution**.
