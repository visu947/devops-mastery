# Lab 09 - CronJobs

## Difficulty

⭐⭐ Intermediate

## Estimated Time

25–35 minutes

---

# CKA Objectives Covered

* Create CronJobs
* Monitor scheduled Jobs
* Verify CronJob execution
* Understand cron schedules

---

# Objective

In this lab, you will:

* Create a CronJob.
* Understand cron scheduling.
* Observe CronJob execution.
* Verify Jobs created by the CronJob.
* Learn common production use cases.

---

# Architecture

```mermaid
flowchart TD

CronJob

↓

Scheduled Time

↓

Job

↓

Pod

↓

Completed
```

---

# What is a CronJob?

A CronJob creates Jobs on a schedule.

Unlike a Job:

* A Job runs once.
* A CronJob creates Jobs repeatedly according to a cron schedule.

Typical use cases:

* Database backups
* Log cleanup
* Report generation
* Cache cleanup
* Certificate renewal

---

# Cron Format

```text
* * * * *
│ │ │ │ │
│ │ │ │ └── Day of Week (0–7)
│ │ │ └──── Month
│ │ └────── Day of Month
│ └──────── Hour
└────────── Minute
```

Example:

```text
0 2 * * *
```

Runs every day at **2:00 AM**.

---

# Step 1 - Create the YAML

Create:

```text
cronjob.yaml
```

Paste:

```yaml
apiVersion: batch/v1
kind: CronJob

metadata:
  name: hello-cron

spec:

  schedule: "*/1 * * * *"

  jobTemplate:

    spec:

      template:

        spec:

          restartPolicy: Never

          containers:

          - name: hello

            image: busybox

            command:

            - sh

            - -c

            - |

              echo "CronJob Started"

              date

              echo "CronJob Finished"
```

---

# Step 2 - Deploy

```bash
kubectl apply -f cronjob.yaml
```

Verify:

```bash
kubectl get cronjobs
```

Expected:

```text
NAME          SCHEDULE

hello-cron    */1 * * * *
```

---

# Step 3 - Wait for Execution

Since the schedule runs every minute:

```bash
kubectl get jobs -w
```

Observe:

A new Job appears approximately every minute.

Stop watching:

```text
Ctrl + C
```

---

# Step 4 - View Pods

```bash
kubectl get pods
```

Notice:

Each Job creates its own Pod.

Completed Pods remain until cleaned up.

---

# Step 5 - View Logs

List Pods:

```bash
kubectl get pods
```

Select one completed Pod:

```bash
kubectl logs <pod-name>
```

Expected:

```text
CronJob Started

<current date>

CronJob Finished
```

---

# Step 6 - Describe the CronJob

```bash
kubectl describe cronjob hello-cron
```

Observe:

* Schedule
* Last Schedule Time
* Active Jobs
* Events

---

# Step 7 - Suspend the CronJob

Suspend execution:

```bash
kubectl patch cronjob hello-cron \
-p '{"spec":{"suspend":true}}'
```

Verify:

```bash
kubectl get cronjob hello-cron -o yaml
```

Locate:

```yaml
suspend: true
```

Wait two minutes.

Verify that no new Jobs are created:

```bash
kubectl get jobs
```

---

# Step 8 - Resume the CronJob

```bash
kubectl patch cronjob hello-cron \
-p '{"spec":{"suspend":false}}'
```

Observe:

New Jobs begin appearing again according to the schedule.

---

# Verification Checklist

✅ CronJob created.

✅ Jobs created automatically.

✅ Pods completed successfully.

✅ Logs viewed.

✅ Suspend/resume verified.

---

# Common Errors

## CronJob Never Runs

Investigate:

```bash
kubectl describe cronjob hello-cron

kubectl get jobs

kubectl get events
```

Possible causes:

* Invalid cron expression
* CronJob suspended
* Controller issue

---

## Jobs Fail

Investigate:

```bash
kubectl describe job <job-name>

kubectl logs <pod-name>
```

---

# Production Discussion

CronJobs commonly perform:

* Nightly backups
* Cleanup tasks
* Report generation
* Database optimization
* Certificate renewal
* Security scans

Avoid scheduling multiple resource-intensive CronJobs to start at the same time.

---

# Knowledge Check

1. What is the difference between a Job and a CronJob?
2. What does `"*/5 * * * *"` mean?
3. How do you suspend a CronJob?
4. Does a CronJob run continuously?
5. What Kubernetes resource does a CronJob create?

---

# Cleanup

```bash
kubectl delete cronjob hello-cron
```

Optionally remove completed Jobs:

```bash
kubectl delete jobs --all
```

Verify:

```bash
kubectl get cronjobs

kubectl get jobs
```

---

# Challenge

1. Create a CronJob that runs every two minutes.
2. Verify multiple Jobs are created.
3. Suspend the CronJob.
4. Confirm no new Jobs are scheduled.
5. Resume the CronJob.
6. Explain the relationship between a CronJob, Job, and Pod.
