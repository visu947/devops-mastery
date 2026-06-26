# Cluster Maintenance - Cheat Sheet

> **Quick revision guide for Kubernetes Cluster Maintenance concepts, workflows, commands, and interview tips.**

---

# Cluster Maintenance Workflow

```text
Plan Maintenance
        │
        ▼
Backup etcd
        │
        ▼
Cordon Node
        │
        ▼
Drain Node
        │
        ▼
Upgrade / Repair
        │
        ▼
Restart Services
        │
        ▼
Uncordon Node
        │
        ▼
Verify Cluster Health
```

Remember:

* Backup first.
* Upgrade carefully.
* Verify after every step.

---

# Node Maintenance Commands

| Command                        | Purpose                 |
| ------------------------------ | ----------------------- |
| `kubectl cordon <node>`        | Mark node unschedulable |
| `kubectl drain <node>`         | Safely evict workloads  |
| `kubectl uncordon <node>`      | Return node to service  |
| `kubectl get nodes`            | View node status        |
| `kubectl describe node <node>` | Inspect node details    |

---

# Cordon vs Drain vs Uncordon

| Command    | Existing Pods                          | New Pods        |
| ---------- | -------------------------------------- | --------------- |
| `cordon`   | Continue running                       | ❌ Not scheduled |
| `drain`    | Evicted (except DaemonSets by default) | ❌ Not scheduled |
| `uncordon` | Normal scheduling resumes              | ✅ Scheduled     |

---

# etcd

## What does etcd store?

* Pods
* Deployments
* Services
* ConfigMaps
* Secrets
* RBAC
* PVCs
* Cluster state

---

## etcd Backup

```bash
ETCDCTL_API=3 etcdctl snapshot save snapshot.db
```

---

## Verify Backup

```bash
ETCDCTL_API=3 etcdctl snapshot status snapshot.db
```

---

## Restore

```bash
ETCDCTL_API=3 etcdctl snapshot restore snapshot.db
```

---

# Kubernetes Upgrade Order

```text
Backup etcd

↓

Upgrade Control Plane

↓

Verify Control Plane

↓

Drain Worker

↓

Upgrade Worker

↓

Restart kubelet

↓

Uncordon Worker

↓

Repeat
```

Never upgrade worker nodes before the control plane.

---

# kubeadm Commands

```bash
kubeadm upgrade plan

kubeadm upgrade apply <version>

kubeadm upgrade node
```

---

# kubelet Commands

```bash
systemctl status kubelet

systemctl restart kubelet

systemctl enable kubelet
```

---

# Certificate Commands

Check expiration:

```bash
kubeadm certs check-expiration
```

Renew all:

```bash
kubeadm certs renew all
```

Renew API Server certificate:

```bash
kubeadm certs renew apiserver
```

---

# Version Skew

General guidelines:

| Component                | Supported Skew                           |
| ------------------------ | ---------------------------------------- |
| `kubectl` ↔ API Server   | ±1 minor version                         |
| kubelet ↔ API Server     | Within supported Kubernetes version skew |
| Control Plane Components | Keep on compatible versions              |

Always consult the official Kubernetes Version Skew Policy before major upgrades.

---

# Cluster Verification

```bash
kubectl cluster-info

kubectl get nodes

kubectl get pods -A

kubectl get pods -n kube-system

kubectl get events --sort-by=.lastTimestamp
```

---

# Logs

kubelet:

```bash
journalctl -u kubelet

journalctl -u kubelet -f
```

---

# Health Checks

Verify:

* API Server reachable
* Nodes Ready
* kube-system Pods Running
* Workloads Healthy
* No critical events

---

# Upgrade Checklist

✅ Backup etcd

✅ Check upgrade plan

✅ Upgrade control plane

✅ Verify cluster

✅ Drain worker

✅ Upgrade worker

✅ Restart kubelet

✅ Uncordon worker

✅ Verify workloads

---

# Common Maintenance Problems

| Problem             | Likely Cause                             |
| ------------------- | ---------------------------------------- |
| Node NotReady       | kubelet stopped or node issue            |
| Pods stuck Pending  | Node still cordoned                      |
| Drain hangs         | PodDisruptionBudget or unmanaged Pods    |
| Upgrade failed      | Version incompatibility or skipped steps |
| Certificate expired | Certificates not renewed                 |
| API unavailable     | Control plane issue                      |
| etcd restore failed | Incorrect snapshot or restore path       |

---

# Production Best Practices

* Back up etcd before upgrades.
* Upgrade one node at a time.
* Verify workloads after each maintenance step.
* Rotate certificates before expiration.
* Follow the Version Skew Policy.
* Test upgrades in staging first.
* Maintain rollback procedures.
* Monitor cluster health throughout maintenance.

---

# Interview Tips

### Why use `cordon`?

Prevent new Pods from being scheduled while keeping existing Pods running.

---

### Why use `drain`?

Safely evict workloads before maintenance or upgrades.

---

### Why use `uncordon`?

Return the node to normal scheduling after maintenance.

---

### Why back up etcd?

etcd stores the entire Kubernetes cluster state. A recent backup is essential for disaster recovery.

---

### Why upgrade the control plane first?

Worker nodes depend on the control plane. Upgrading the control plane first maintains compatibility.

---

# CKA Memory Map

```text
Node Maintenance
       │
       ▼
Cordon
       │
       ▼
Drain
       │
       ▼
Uncordon
       │
       ▼
etcd Backup
       │
       ▼
Certificate Management
       │
       ▼
Cluster Upgrade
       │
       ▼
Version Skew
       │
       ▼
Health Verification
```

---

# Rapid Revision

```bash
kubectl cordon <node>

kubectl drain <node> --ignore-daemonsets

kubectl uncordon <node>

kubectl get nodes

kubectl cluster-info

kubectl get pods -A

kubeadm upgrade plan

kubeadm certs check-expiration

systemctl restart kubelet

journalctl -u kubelet
```
