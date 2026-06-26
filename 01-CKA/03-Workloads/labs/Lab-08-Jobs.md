# Lab 08 - Jobs

## Difficulty

⭐⭐ Intermediate

## Estimated Time

20–30 minutes

---

# CKA Objectives Covered

* Create Jobs
* Monitor Job execution
* Inspect completed Jobs
* Understand retry behavior

---

# Objective

In this lab, you will:

* Create a Kubernetes Job.
* Observe Job execution.
* Monitor Job completion.
* Understand restart and retry behavior.
* Inspect completed Pods.

---

# Architecture

```mermaid
flowchart TD

Job

↓

Pod

↓

Task Running

↓

Completed

↓

Job Succeeded
```

---

# What is a Job?

A Job creates one or more Pods to perform a task.

Unlike a Deployment:

* The task completes.
* The Pod exits.
* The Job succeeds.

Typical use cases include:

* Database migration
* Backup
* Data import
* Batch processing

---

# Step 1 - Create the YAML

Create:

```text
job.yaml
```

Paste:

```yaml
apiVersion: batch/v1
kind: Job

metadata:
  name: hello-job

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
          echo "Starting Job..."
          sleep 5
          echo "Job Completed Successfully!"
```

---

# Step 2 - Deploy

```bash
kubectl apply -f job.yaml
```

Verify:

```bash
kubectl get jobs

kubectl get pods
```

---

# Step 3 - Watch Completion

```bash
kubectl get jobs -w
```

Expected:

```text
COMPLETIONS

1/1
```

---

# Step 4 - View Logs

List Pods:

```bash
kubectl get pods
```

View logs:

```bash
kubectl logs <job-pod-name>
```

Expected:

```text
Starting Job...

Job Completed Successfully!
```

---

# Step 5 - Describe the Job

```bash
kubectl describe job hello-job
```

Observe:

* Completions
* Duration
* Events

---

# Step 6 - View Job YAML

```bash
kubectl get job hello-job -o yaml
```

Locate:

* Completions
* Parallelism
* Template

---

# Step 7 - Failed Job Example

Modify the Job command:

```yaml
command:

- sh

- -c

- |

  exit 1
```

Deploy again.

Observe:

```bash
kubectl get jobs

kubectl describe job hello-job
```

Notice:

The Job retries according to its configuration.

---

# Verification Checklist

✅ Job created.

✅ Pod completed.

✅ Logs viewed.

✅ Job status verified.

---

# Common Errors

## Job Never Completes

Investigate:

```bash
kubectl describe job hello-job

kubectl logs <pod-name>

kubectl get events
```

Possible causes:

* Application error
* Infinite loop
* Missing dependency

---

## Job Keeps Retrying

Investigate:

```bash
kubectl describe job hello-job
```

Check:

```yaml
backoffLimit
```

---

# Production Discussion

Jobs are commonly used for:

* Database migrations
* Data imports
* Nightly processing
* Report generation
* One-time maintenance

Jobs should be idempotent whenever possible.

---

# Knowledge Check

1. What is the difference between a Deployment and a Job?
2. What happens after a Job completes?
3. Why is `restartPolicy: Never` commonly used?
4. What controls Job retries?
5. Name three production use cases for Jobs.

---

# Cleanup

```bash
kubectl delete job hello-job
```

Verify:

```bash
kubectl get jobs
```

---

# Challenge

1. Create a Job.
2. Verify it completes successfully.
3. View the logs.
4. Modify it to fail.
5. Observe retry behavior.
6. Explain the purpose of `backoffLimit`.
