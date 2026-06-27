# Troubleshooting - Cheat Sheet

> **Quick revision guide for Kubernetes troubleshooting during the CKA exam and production incidents.**

---

# Universal Troubleshooting Workflow

```text
Problem Reported

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

Never guess.

Always collect evidence before making changes.

---

# First Commands to Run

```bash
kubectl get pods -A

kubectl get nodes

kubectl cluster-info

kubectl get events --sort-by=.lastTimestamp
```

---

# Pod Troubleshooting

## Check Pod Status

```bash
kubectl get pods
```

---

## Describe Pod

```bash
kubectl describe pod <pod-name>
```

---

## View Logs

```bash
kubectl logs <pod-name>
```

---

## Previous Logs

```bash
kubectl logs <pod-name> --previous
```

Useful for:

* CrashLoopBackOff

---

## Execute Into Pod

```bash
kubectl exec -it <pod-name> -- sh
```

---

# Common Pod States

| Status           | Meaning                           |
| ---------------- | --------------------------------- |
| Pending          | Scheduling problem                |
| Running          | Healthy                           |
| CrashLoopBackOff | Application crashing repeatedly   |
| ImagePullBackOff | Image pull failure                |
| ErrImagePull     | Invalid image or registry problem |
| Completed        | Job finished successfully         |
| OOMKilled        | Out of memory                     |

---

# Scheduling

Check:

```bash
kubectl describe pod <pod-name>
```

Look for:

* NodeSelector
* Taints
* Tolerations
* Affinity
* Resources

---

Check Nodes

```bash
kubectl get nodes
```

```bash
kubectl describe node <node-name>
```

---

# Networking

List Services

```bash
kubectl get svc
```

Endpoints

```bash
kubectl get endpoints
```

EndpointSlices

```bash
kubectl get endpointslice
```

Pod Labels

```bash
kubectl get pods --show-labels
```

---

# DNS

CoreDNS

```bash
kubectl get pods -n kube-system
```

DNS Test

```bash
nslookup <service-name>
```

Resolver

```bash
cat /etc/resolv.conf
```

---

# Storage

PVC

```bash
kubectl get pvc

kubectl describe pvc <pvc-name>
```

PV

```bash
kubectl get pv
```

StorageClass

```bash
kubectl get sc
```

---

# Security

Permissions

```bash
kubectl auth can-i get pods
```

ServiceAccounts

```bash
kubectl get sa
```

Secrets

```bash
kubectl get secrets
```

---

# Node Troubleshooting

Nodes

```bash
kubectl get nodes
```

Describe

```bash
kubectl describe node <node-name>
```

kubelet

```bash
systemctl status kubelet

journalctl -u kubelet
```

---

# Control Plane

```bash
kubectl get pods -n kube-system
```

```bash
kubectl cluster-info
```

Static Pods

```bash
ls /etc/kubernetes/manifests
```

---

# Resource Usage

```bash
kubectl top nodes

kubectl top pods
```

Requires Metrics Server.

---

# Events

```bash
kubectl get events --sort-by=.lastTimestamp
```

Always read Events before changing manifests.

---

# Production Checklist

✔ Check Pods

✔ Check Nodes

✔ Check Events

✔ Check Logs

✔ Check Services

✔ Check Endpoints

✔ Check DNS

✔ Check Storage

✔ Check Security

✔ Verify Fix

---

# Common Problems

| Problem          | Check                |
| ---------------- | -------------------- |
| Pending          | Scheduler, Nodes     |
| CrashLoopBackOff | Logs, Events         |
| ImagePullBackOff | Image, Registry      |
| DNS Failure      | CoreDNS              |
| Service Failure  | Selectors, Endpoints |
| PVC Pending      | StorageClass         |
| Forbidden        | RBAC                 |
| Secret Missing   | Secrets              |
| Node NotReady    | kubelet              |
| API Server Issue | kube-system          |

---

# CKA Memory Map

```text
Problem

↓

Pod

↓

Node

↓

Network

↓

Storage

↓

Security

↓

Control Plane

↓

Fix

↓

Verify
```

---

# Golden Rule

Never troubleshoot by guessing.

Always follow this order:

1. Observe
2. Describe
3. Check Events
4. Check Logs
5. Identify Root Cause
6. Apply Fix
7. Verify

---

# 30-Second CKA Revision

```bash
kubectl get pods -A

kubectl describe pod <pod>

kubectl logs <pod>

kubectl logs <pod> --previous

kubectl get events --sort-by=.lastTimestamp

kubectl get nodes

kubectl describe node <node>

kubectl get svc

kubectl get endpoints

kubectl get pvc

kubectl auth can-i get pods

kubectl cluster-info
```

---

# Exam Tips

* Read **Events** before editing YAML.
* Use **describe** before **logs** when diagnosing resource issues.
* Use **logs --previous** for restarted containers.
* Verify labels and selectors for Service problems.
* Check PVC status before debugging application storage.
* Always confirm the fix before moving to the next task.
