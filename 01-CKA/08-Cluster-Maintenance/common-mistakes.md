# Cluster Maintenance - Common Mistakes

> **Learn the most common Kubernetes cluster maintenance mistakes, why they happen, and how to avoid them in production.**

---

# 1. Skipping the etcd Backup

## Mistake

Performing cluster upgrades or major maintenance without creating an etcd backup.

## Why It Happens

* Trying to save time.
* Assuming upgrades will always succeed.

## Problem

If the control plane becomes corrupted or the upgrade fails, there may be no reliable way to restore the cluster state.

## Correct Approach

Always create and verify an etcd snapshot before:

* Kubernetes upgrades
* Control plane maintenance
* Certificate operations
* Major cluster changes

---

# 2. Upgrading Worker Nodes Before the Control Plane

## Mistake

Upgrading worker nodes first.

## Problem

This may create unsupported version combinations and compatibility issues.

## Correct Approach

Always follow this order:

```text
Backup etcd
      ↓
Upgrade Control Plane
      ↓
Verify Cluster
      ↓
Upgrade Worker Nodes
```

---

# 3. Forgetting to Drain a Node

## Mistake

Stopping kubelet or rebooting a node while workloads are still running.

## Problem

Applications may terminate unexpectedly, causing unnecessary downtime.

## Correct Approach

Always:

```bash
kubectl cordon <node>

kubectl drain <node> --ignore-daemonsets
```

before performing maintenance.

---

# 4. Forgetting to Uncordon the Node

## Mistake

Completing maintenance but leaving the node cordoned.

## Problem

* No new Pods are scheduled.
* Cluster capacity is reduced.
* Scheduling becomes unbalanced.

## Correct Approach

Run:

```bash
kubectl uncordon <node>
```

Verify the node is `Ready` and schedulable.

---

# 5. Ignoring PodDisruptionBudgets

## Mistake

Using `kubectl drain` without considering PodDisruptionBudgets (PDBs).

## Problem

Drain operations may hang or fail because Kubernetes protects application availability.

## Correct Approach

Review existing PDBs before draining production nodes and plan maintenance accordingly.

---

# 6. Using `--force` Without Understanding the Impact

## Mistake

Running:

```bash
kubectl drain --force
```

without understanding why it is needed.

## Problem

Unmanaged Pods may be removed unexpectedly.

## Correct Approach

Use `--force` only when necessary and understand which workloads will be affected.

---

# 7. Ignoring Version Compatibility

## Mistake

Skipping the Kubernetes Version Skew Policy.

## Problem

Unsupported component versions can lead to upgrade failures or unstable clusters.

## Correct Approach

Review compatibility before upgrading:

* API Server
* kubelet
* `kubectl`
* Control plane components

---

# 8. Not Checking Certificate Expiration

## Mistake

Ignoring certificate expiration dates.

## Problem

Expired certificates can prevent communication between Kubernetes components.

## Correct Approach

Regularly check:

```bash
kubeadm certs check-expiration
```

Renew certificates before they expire.

---

# 9. Skipping Health Verification

## Mistake

Assuming maintenance succeeded without checking cluster health.

## Problem

Hidden failures may go unnoticed until applications begin failing.

## Correct Approach

Verify:

```bash
kubectl get nodes

kubectl get pods -A

kubectl get pods -n kube-system

kubectl get events
```

---

# 10. Upgrading Multiple Nodes at Once

## Mistake

Upgrading several worker nodes simultaneously.

## Problem

This increases operational risk and can reduce application availability.

## Correct Approach

Upgrade one worker node at a time.

Verify cluster health before proceeding to the next node.

---

# 11. Restarting kubelet Without Verification

## Mistake

Restarting kubelet without confirming it returns to a healthy state.

## Problem

The node may remain `NotReady`.

## Correct Approach

After restarting:

```bash
systemctl status kubelet

kubectl get nodes
```

Verify the node reports `Ready`.

---

# 12. Ignoring kube-system Pods

## Mistake

Checking only application namespaces after maintenance.

## Problem

Control plane or system components may have failed even though application Pods appear healthy.

## Correct Approach

Always verify:

```bash
kubectl get pods -n kube-system
```

---

# Common Maintenance Mistakes

| Mistake                | Better Practice                 |
| ---------------------- | ------------------------------- |
| Skip etcd backup       | Backup before maintenance       |
| Upgrade workers first  | Upgrade control plane first     |
| Forget to drain        | Drain before maintenance        |
| Forget to uncordon     | Return node to service          |
| Ignore PDBs            | Review disruption budgets       |
| Overuse `--force`      | Use only when necessary         |
| Ignore version skew    | Follow compatibility guidelines |
| Ignore certificates    | Monitor expiration              |
| Skip health checks     | Verify after every step         |
| Upgrade multiple nodes | Upgrade one node at a time      |

---

# Production Best Practices

* Always create an etcd backup before upgrades.
* Upgrade the control plane first.
* Drain nodes before maintenance.
* Upgrade one worker node at a time.
* Verify cluster health after every operation.
* Follow the Version Skew Policy.
* Monitor certificate expiration.
* Keep rollback procedures documented.
* Test upgrades in staging before production.
* Schedule maintenance during approved maintenance windows.

---

# Interview Perspective

A common interview question is:

> **"What are the biggest mistakes teams make during Kubernetes maintenance?"**

A strong answer includes:

1. Skipping etcd backups.
2. Upgrading worker nodes before the control plane.
3. Forgetting to drain or uncordon nodes.
4. Ignoring PodDisruptionBudgets.
5. Overusing `kubectl drain --force`.
6. Ignoring certificate expiration.
7. Skipping post-maintenance health checks.
8. Ignoring the Version Skew Policy.
9. Upgrading multiple nodes simultaneously.
10. Failing to verify `kube-system` components after maintenance.

These mistakes are preventable with a disciplined maintenance process and proper verification after every step.
