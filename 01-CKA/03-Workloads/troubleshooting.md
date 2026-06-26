# Workloads - Troubleshooting Guide

> This guide provides a structured approach to troubleshooting Kubernetes workloads in production.

---

# Troubleshooting Workflow

Always follow this sequence:

```text
kubectl get deployments
        │
        ▼
kubectl rollout status
        │
        ▼
kubectl describe deployment
        │
        ▼
kubectl get replicasets
        │
        ▼
kubectl get pods
        │
        ▼
kubectl describe pod
        │
        ▼
kubectl logs
        │
        ▼
kubectl get events
```

Never start by deleting Pods. Find the root cause first.

---

# Scenario 1 - Deployment Rollout Stuck

## Symptoms

```text
kubectl rollout status deployment/nginx

Waiting for deployment "nginx" rollout to finish...
```

## Investigation

```bash
kubectl rollout status deployment/nginx

kubectl describe deployment nginx

kubectl get rs

kubectl get pods

kubectl get events --sort-by=.lastTimestamp
```

## Possible Causes

* Readiness probe failures
* Image pull failures
* Insufficient cluster resources
* Scheduling failures

## Resolution

Fix the underlying issue and allow the rollout to continue.

## Prevention

Always validate deployments in a staging environment before production.

---

# Scenario 2 - Deployment Not Creating Pods

## Symptoms

```text
DESIRED   CURRENT   READY

3         0         0
```

## Investigation

```bash
kubectl describe deployment nginx

kubectl get rs

kubectl describe rs
```

## Possible Causes

* Invalid selector
* Admission controller rejection
* Resource quota exceeded

## Resolution

Correct the selector or deployment specification.

---

# Scenario 3 - ReplicaSet Not Creating New Pods

## Symptoms

Replica count is lower than expected.

## Investigation

```bash
kubectl describe rs

kubectl get events
```

## Possible Causes

* Insufficient resources
* Scheduler failures
* Node unavailable

---

# Scenario 4 - Pods Not Updating After Image Change

## Symptoms

Deployment updated, but Pods still run the old image.

## Investigation

```bash
kubectl rollout history deployment/nginx

kubectl describe deployment nginx

kubectl get pods -o wide
```

## Possible Causes

* Image tag not changed (`latest`)
* Rollout paused
* Image pull cache

## Resolution

Use versioned image tags and verify rollout status.

---

# Scenario 5 - Rollback Fails

## Symptoms

```bash
kubectl rollout undo deployment/nginx
```

does not restore the application.

## Investigation

```bash
kubectl rollout history deployment/nginx

kubectl describe deployment nginx
```

## Possible Causes

* Revision unavailable
* Configuration drift
* Image removed from registry

---

# Scenario 6 - DaemonSet Missing on Some Nodes

## Symptoms

Not every node has a DaemonSet Pod.

## Investigation

```bash
kubectl get ds

kubectl describe ds

kubectl get nodes

kubectl describe node <node-name>
```

## Possible Causes

* Node selector
* Taints and tolerations
* Node NotReady

---

# Scenario 7 - StatefulSet Pod Stuck in Pending

## Symptoms

```text
web-0

Pending
```

## Investigation

```bash
kubectl describe pod web-0

kubectl get pvc

kubectl get pv

kubectl get storageclass
```

## Possible Causes

* Unbound PVC
* Storage unavailable
* Scheduling issue

---

# Scenario 8 - Job Never Completes

## Symptoms

Job remains active.

## Investigation

```bash
kubectl get jobs

kubectl describe job backup-job

kubectl logs <pod-name>
```

## Possible Causes

* Application failure
* Infinite loop
* Missing dependency

---

# Scenario 9 - CronJob Never Runs

## Symptoms

Scheduled Job never starts.

## Investigation

```bash
kubectl get cronjobs

kubectl describe cronjob nightly-backup

kubectl get jobs
```

## Possible Causes

* Invalid cron schedule
* CronJob suspended
* Time zone assumptions
* Controller issue

---

# Scenario 10 - Scaling Has No Effect

## Symptoms

```bash
kubectl scale deployment nginx --replicas=5
```

Pods remain unchanged.

## Investigation

```bash
kubectl describe deployment nginx

kubectl get rs

kubectl get pods
```

## Possible Causes

* Resource constraints
* Failed scheduling
* ReplicaSet issue

---

# Production Troubleshooting Checklist

1. Check workload status.

```bash
kubectl get deploy

kubectl get rs

kubectl get ds

kubectl get sts
```

2. Check rollout.

```bash
kubectl rollout status deployment/<name>
```

3. Inspect workload.

```bash
kubectl describe deployment <name>
```

4. Inspect ReplicaSet.

```bash
kubectl describe rs
```

5. Inspect Pods.

```bash
kubectl describe pod <pod>
```

6. Review logs.

```bash
kubectl logs <pod>
```

7. Review events.

```bash
kubectl get events --sort-by=.lastTimestamp
```

---

# Production Tips

* Never delete Pods without understanding why they failed.
* Always check rollout status after deployments.
* Avoid using the `latest` image tag.
* Keep previous ReplicaSets for rollback.
* Monitor deployments during production releases.

---

# Interviewer's Perspective

A common senior-level interview question is:

> "A deployment is stuck during a rolling update. Walk me through your troubleshooting process."

A strong answer includes:

1. Check rollout status.
2. Describe the Deployment.
3. Inspect ReplicaSets.
4. Inspect affected Pods.
5. Review Events.
6. Review logs.
7. Identify the root cause.
8. Roll back if necessary.
9. Validate recovery.
10. Document the incident and preventive action.
