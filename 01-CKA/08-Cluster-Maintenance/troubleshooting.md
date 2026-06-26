# Cluster Maintenance - Troubleshooting Guide

> **Systematic approach to troubleshooting Kubernetes cluster maintenance issues in production and the CKA exam.**

---

# Cluster Maintenance Troubleshooting Workflow

```text
Maintenance Issue
        │
        ▼
Check Cluster Health
        │
        ▼
Check Nodes
        │
        ▼
Check kube-system Pods
        │
        ▼
Check Events
        │
        ▼
Check kubelet
        │
        ▼
Check etcd
        │
        ▼
Check Certificates
        │
        ▼
Root Cause
```

---

# Scenario 1 - Node is NotReady

## Symptoms

```text
STATUS

NotReady
```

---

## Investigation

```bash
kubectl get nodes

kubectl describe node <node-name>

systemctl status kubelet

journalctl -u kubelet -n 100
```

---

## Common Causes

* kubelet stopped
* Container runtime unavailable
* Network issue
* Node rebooted
* Disk pressure
* Memory pressure

---

## Resolution

Verify:

* kubelet is running.
* Container runtime is healthy.
* Node can reach the API Server.
* Node conditions are healthy.

---

# Scenario 2 - Node Stuck in Ready,SchedulingDisabled

## Symptoms

```text
Ready,SchedulingDisabled
```

---

## Investigation

```bash
kubectl get nodes
```

---

## Cause

The node is still cordoned.

---

## Resolution

```bash
kubectl uncordon <node-name>
```

Verify:

```bash
kubectl get nodes
```

---

# Scenario 3 - Drain Command Hangs

## Symptoms

```text
kubectl drain
```

never completes.

---

## Investigation

```bash
kubectl get pdb -A

kubectl get pods -A -o wide
```

---

## Common Causes

* PodDisruptionBudget
* Unmanaged Pods
* DaemonSet Pods
* Long termination grace period

---

## Resolution

Review:

* PodDisruptionBudgets.
* Unmanaged Pods.
* Drain options such as `--ignore-daemonsets` when appropriate.

---

# Scenario 4 - Pods Remain Pending After Maintenance

## Investigation

```bash
kubectl get pods -A

kubectl describe pod <pod-name>

kubectl get nodes
```

---

## Common Causes

* Node still cordoned.
* Insufficient resources.
* Taints.
* Scheduling constraints.

---

## Resolution

Verify:

* Node is uncordoned.
* Resources are available.
* Scheduler events.

---

# Scenario 5 - kubeadm Upgrade Failed

## Investigation

```bash
kubeadm upgrade plan

kubectl get nodes

kubectl get pods -n kube-system
```

---

## Common Causes

* Version incompatibility.
* Upgrade skipped steps.
* Control plane unhealthy.

---

## Resolution

Review:

* Kubernetes release notes.
* Version compatibility.
* kube-system Pods.
* Control plane health.

---

# Scenario 6 - etcd Backup Failed

## Symptoms

```text
snapshot save failed
```

---

## Investigation

Verify:

* etcd endpoint.
* Certificate paths.
* Network connectivity.
* Disk space.

Example:

```bash
ETCDCTL_API=3 etcdctl endpoint health \
  --endpoints=https://127.0.0.1:2379
```

---

## Resolution

Correct certificate paths and verify endpoint health before retrying.

---

# Scenario 7 - etcd Restore Failed

## Investigation

```bash
ETCDCTL_API=3 etcdctl snapshot status snapshot.db
```

Verify:

* Snapshot integrity.
* Restore location.
* etcd configuration.

---

## Resolution

Restore to the correct data directory and update the etcd configuration if required.

---

# Scenario 8 - Certificate Expired

## Symptoms

```text
x509 certificate has expired
```

---

## Investigation

```bash
kubeadm certs check-expiration
```

---

## Resolution

Renew certificates:

```bash
kubeadm certs renew all
```

Restart affected components if required.

---

# Scenario 9 - kubelet Will Not Start

## Investigation

```bash
systemctl status kubelet

journalctl -u kubelet
```

---

## Common Causes

* Invalid kubelet configuration.
* Certificate issue.
* Missing runtime.
* Corrupted configuration.

---

## Resolution

Review logs and configuration, then restart kubelet after correcting the issue.

---

# Scenario 10 - Cluster Not Healthy After Upgrade

## Investigation

```bash
kubectl cluster-info

kubectl get nodes

kubectl get pods -A

kubectl get pods -n kube-system

kubectl get events --sort-by=.lastTimestamp
```

---

## Verify

* API Server
* Scheduler
* Controller Manager
* CoreDNS
* kube-proxy
* CNI plugin
* Application Pods

---

# Common Maintenance Commands

```bash
kubectl get nodes

kubectl describe node <node-name>

kubectl cordon <node-name>

kubectl drain <node-name> --ignore-daemonsets

kubectl uncordon <node-name>

kubectl cluster-info

kubectl get pods -A

kubectl get pods -n kube-system

kubectl get events --sort-by=.lastTimestamp

kubeadm upgrade plan

kubeadm certs check-expiration

systemctl status kubelet

journalctl -u kubelet
```

---

# Maintenance Checklist

Before maintenance:

* Verify cluster health.
* Backup etcd.
* Review upgrade plan.
* Schedule maintenance window.

During maintenance:

* Cordon node.
* Drain node.
* Perform maintenance.
* Monitor kubelet.

After maintenance:

* Uncordon node.
* Verify nodes are `Ready`.
* Verify `kube-system` Pods.
* Verify application workloads.
* Review events.

---

# Common Problems

| Problem                  | Likely Cause                          |
| ------------------------ | ------------------------------------- |
| Node NotReady            | kubelet or node issue                 |
| Ready,SchedulingDisabled | Node still cordoned                   |
| Drain hangs              | PodDisruptionBudget or unmanaged Pods |
| Pods Pending             | Node unavailable or scheduling issue  |
| Upgrade failed           | Version incompatibility               |
| etcd backup failed       | Endpoint or certificate issue         |
| etcd restore failed      | Invalid snapshot or restore path      |
| Certificate expired      | Certificates not renewed              |
| kubelet won't start      | Configuration or certificate problem  |

---

# Production Tips

* Never upgrade without an etcd backup.
* Upgrade one worker node at a time.
* Verify the control plane before touching workers.
* Always check `kube-system` after maintenance.
* Monitor kubelet logs if a node becomes `NotReady`.
* Verify events after every maintenance activity.
* Keep rollback procedures documented and tested.

---

# CKA Exam Tips

If maintenance causes problems:

1. Check node status.
2. Verify kube-system Pods.
3. Review events.
4. Inspect kubelet logs.
5. Verify certificates.
6. Confirm the node is uncordoned.
7. Ensure workloads have rescheduled successfully.

Following a consistent troubleshooting workflow is much faster than changing configuration by trial and error.
