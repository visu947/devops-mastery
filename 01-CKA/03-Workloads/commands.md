# Workloads - Commands

This document contains the most commonly used commands for managing Kubernetes workloads.

---

# Deployments

## Create Deployment

```bash
kubectl create deployment nginx --image=nginx
```

---

## Generate Deployment YAML

```bash
kubectl create deployment nginx \
  --image=nginx \
  --dry-run=client \
  -o yaml
```

---

## Apply Deployment

```bash
kubectl apply -f deployment.yaml
```

---

## List Deployments

```bash
kubectl get deployments
```

Short form:

```bash
kubectl get deploy
```

---

## Describe Deployment

```bash
kubectl describe deployment nginx
```

---

## View Deployment YAML

```bash
kubectl get deployment nginx -o yaml
```

---

# Scaling

Scale manually:

```bash
kubectl scale deployment nginx --replicas=5
```

Verify:

```bash
kubectl get deployment
```

---

# Rolling Updates

Update image:

```bash
kubectl set image deployment/nginx \
nginx=nginx:1.27
```

---

Watch rollout:

```bash
kubectl rollout status deployment/nginx
```

---

View rollout history:

```bash
kubectl rollout history deployment/nginx
```

---

Rollback:

```bash
kubectl rollout undo deployment/nginx
```

Rollback to revision:

```bash
kubectl rollout undo deployment/nginx \
--to-revision=2
```

---

Restart Deployment

```bash
kubectl rollout restart deployment/nginx
```

---

Pause Rollout

```bash
kubectl rollout pause deployment/nginx
```

---

Resume Rollout

```bash
kubectl rollout resume deployment/nginx
```

---

# ReplicaSets

List ReplicaSets:

```bash
kubectl get replicasets
```

Short form:

```bash
kubectl get rs
```

Describe:

```bash
kubectl describe rs
```

---

# DaemonSets

List:

```bash
kubectl get daemonsets
```

Short form:

```bash
kubectl get ds
```

Describe:

```bash
kubectl describe ds
```

---

# StatefulSets

List:

```bash
kubectl get statefulsets
```

Short form:

```bash
kubectl get sts
```

Describe:

```bash
kubectl describe sts
```

Scale:

```bash
kubectl scale sts mysql --replicas=3
```

---

# Jobs

List Jobs:

```bash
kubectl get jobs
```

Describe:

```bash
kubectl describe job backup-job
```

Delete:

```bash
kubectl delete job backup-job
```

---

# CronJobs

List:

```bash
kubectl get cronjobs
```

Short form:

```bash
kubectl get cj
```

Describe:

```bash
kubectl describe cronjob nightly-backup
```

Suspend:

```bash
kubectl patch cronjob nightly-backup \
-p '{"spec":{"suspend":true}}'
```

Resume:

```bash
kubectl patch cronjob nightly-backup \
-p '{"spec":{"suspend":false}}'
```

---

# Pods Created by Workloads

List Pods:

```bash
kubectl get pods
```

Show labels:

```bash
kubectl get pods --show-labels
```

Describe:

```bash
kubectl describe pod <pod-name>
```

---

# Events

```bash
kubectl get events \
--sort-by=.lastTimestamp
```

---

# Namespaces

List Deployments:

```bash
kubectl get deploy -n production
```

List StatefulSets:

```bash
kubectl get sts -n production
```

---

# Output Formats

```bash
kubectl get deploy -o yaml

kubectl get rs -o yaml

kubectl get ds -o yaml

kubectl get sts -o yaml

kubectl get jobs -o yaml

kubectl get cj -o yaml
```

---

# Useful Shortcuts

```bash
alias k=kubectl
```

Explain resources:

```bash
kubectl explain deployment

kubectl explain deployment.spec

kubectl explain deployment.spec.template.spec
```

---

# Production Tips

- Prefer Deployments over standalone Pods.
- Monitor rollout status during upgrades.
- Verify Pods after every rollout.
- Use labels consistently.
- Roll back immediately if a deployment is unhealthy.
- Avoid editing live resources directly; use declarative manifests whenever possible.

---

# References

- Kubernetes Deployments
- Kubernetes ReplicaSets
- Kubernetes StatefulSets
- Kubernetes Jobs
- Kubernetes CronJobs
