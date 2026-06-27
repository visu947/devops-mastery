# Troubleshooting Guide

> **A production-ready Kubernetes troubleshooting playbook for the CKA exam and real-world cluster operations.**

---

# Troubleshooting Philosophy

Effective troubleshooting is not about memorizing fixes.

It is about following a structured process that narrows down the root cause.

Golden rule:

```text id="tph001"
Observe

↓

Collect Evidence

↓

Identify Root Cause

↓

Apply One Fix

↓

Verify
```

Never guess.

---

# Universal Troubleshooting Workflow

```text id="tph002"
Issue Reported

↓

Identify Resource

↓

kubectl get

↓

kubectl describe

↓

Check Events

↓

Check Logs

↓

Check Dependencies

↓

Identify Root Cause

↓

Apply Fix

↓

Verify
```

---

# Step 1 - Verify Cluster Health

Start with the cluster.

```bash id="tph003"
kubectl cluster-info

kubectl get nodes

kubectl get pods -A
```

Verify:

* API Server reachable
* Nodes Ready
* No major failures

---

# Step 2 - Identify the Affected Resource

Determine what is failing.

Examples:

* Pod
* Deployment
* Service
* PVC
* Node
* Ingress

List resources:

```bash id="tph004"
kubectl get all -A
```

---

# Step 3 - Describe the Resource

This is often the most useful command.

```bash id="tph005"
kubectl describe pod <pod-name>
```

or

```bash id="tph006"
kubectl describe deployment <deployment-name>
```

Look for:

* Events
* Conditions
* Scheduling errors
* Mount failures
* Probe failures

---

# Step 4 - Review Events

```bash id="tph007"
kubectl get events --sort-by=.lastTimestamp
```

Common Event messages:

| Event              | Meaning                        |
| ------------------ | ------------------------------ |
| FailedScheduling   | Scheduler issue                |
| FailedMount        | Storage issue                  |
| FailedAttachVolume | Volume problem                 |
| FailedPull         | Image pull failure             |
| BackOff            | Application repeatedly failing |
| Unhealthy          | Probe failure                  |

---

# Step 5 - Review Logs

```bash id="tph008"
kubectl logs <pod-name>
```

For restarting containers:

```bash id="tph009"
kubectl logs <pod-name> --previous
```

Logs help identify:

* Application crashes
* Configuration errors
* Database connection failures
* Missing Secrets
* Missing ConfigMaps

---

# Step 6 - Check Dependencies

Many Kubernetes resources depend on other resources.

## Deployment

```text id="tph010"
Deployment

↓

ReplicaSet

↓

Pod
```

---

## Service

```text id="tph011"
Service

↓

Endpoints

↓

Pod Labels
```

Verify:

```bash id="tph012"
kubectl get svc

kubectl get endpoints

kubectl get endpointslice

kubectl get pods --show-labels
```

---

## Storage

```text id="tph013"
Pod

↓

PVC

↓

PV

↓

StorageClass
```

Verify:

```bash id="tph014"
kubectl get pvc

kubectl get pv

kubectl get sc
```

---

## Security

```text id="tph015"
User

↓

RBAC

↓

ServiceAccount

↓

Secret
```

Verify:

```bash id="tph016"
kubectl auth can-i get pods

kubectl get sa

kubectl get secrets
```

---

# Step 7 - Investigate Nodes

```bash id="tph017"
kubectl get nodes

kubectl describe node <node-name>
```

Look for:

* Ready
* MemoryPressure
* DiskPressure
* PIDPressure
* NetworkUnavailable

On the node:

```bash id="tph018"
systemctl status kubelet

journalctl -u kubelet -n 100
```

---

# Step 8 - Verify Control Plane

```bash id="tph019"
kubectl get pods -n kube-system
```

Verify:

* kube-apiserver
* etcd
* kube-scheduler
* kube-controller-manager
* CoreDNS
* kube-proxy

---

# Step 9 - Apply the Fix

Examples:

* Correct labels.
* Fix selectors.
* Update image.
* Renew certificates.
* Bind storage.
* Correct RBAC.
* Restart kubelet.

Apply only one change at a time.

---

# Step 10 - Verify the Fix

Always verify.

```bash id="tph020"
kubectl get pods -A

kubectl get nodes

kubectl get events --sort-by=.lastTimestamp
```

If applicable:

* Test application access.
* Verify DNS resolution.
* Confirm Services respond.
* Validate storage mounts.

---

# Troubleshooting by Symptom

## Pod Pending

Check:

* Scheduler Events
* Resources
* NodeSelector
* Taints
* PVC

---

## CrashLoopBackOff

Check:

* Logs
* Previous logs
* Events
* Readiness probes
* Liveness probes

---

## ImagePullBackOff

Check:

* Image name
* Registry
* Authentication
* ImagePullSecrets

---

## Service Not Working

Check:

* Selector
* Labels
* Endpoints
* EndpointSlices

---

## DNS Failure

Check:

* CoreDNS
* kube-dns Service
* `/etc/resolv.conf`
* NetworkPolicy

---

## PVC Pending

Check:

* StorageClass
* PV
* Capacity
* Access mode
* CSI Driver

---

## Node NotReady

Check:

* kubelet
* Container runtime
* Disk pressure
* Memory pressure
* Network connectivity

---

## Forbidden Error

Check:

```bash id="tph021"
kubectl auth can-i
```

Review:

* Role
* ClusterRole
* RoleBinding
* ClusterRoleBinding

---

## API Server Unreachable

Check:

* kube-apiserver
* etcd
* kubelet
* Static Pod manifests

---

# Decision Tree

```text id="tph022"
Problem

↓

Pod?

↓

Describe

↓

Events

↓

Logs

↓

Dependencies

↓

Node

↓

Control Plane

↓

Fix

↓

Verify
```

---

# Production Checklist

Before making changes:

✔ Understand the problem.

✔ Collect evidence.

✔ Review Events.

✔ Review Logs.

✔ Check dependencies.

After making changes:

✔ Verify resources.

✔ Verify workloads.

✔ Verify cluster health.

✔ Monitor Events.

---

# Most Valuable Commands

```bash id="tph023"
kubectl get pods -A

kubectl describe pod <pod>

kubectl logs <pod>

kubectl logs <pod> --previous

kubectl get events --sort-by=.lastTimestamp

kubectl get svc

kubectl get endpoints

kubectl get pvc

kubectl get nodes

kubectl describe node <node>

kubectl auth can-i get pods

kubectl cluster-info
```

---

# CKA Troubleshooting Strategy

For every troubleshooting question:

1. Read the task carefully.
2. Identify the affected resource.
3. Run `kubectl get`.
4. Run `kubectl describe`.
5. Review Events.
6. Review Logs.
7. Check dependencies.
8. Apply the smallest required fix.
9. Verify the solution.
10. Move to the next task.

---

# Final Takeaway

The strongest Kubernetes administrators do not rely on guesswork.

They rely on:

```text id="tph024"
Observation

↓

Evidence

↓

Root Cause

↓

Single Fix

↓

Verification
```

Master this workflow, and you'll be prepared for both the **CKA exam** and real-world production troubleshooting.
