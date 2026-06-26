# Scheduling - Cheat Sheet

> **One-page quick revision guide for Kubernetes Scheduling**

---

# Scheduler Flow

```text
Pod Created
      │
      ▼
API Server
      │
      ▼
Scheduler
      │
      ▼
Filter Nodes
      │
      ▼
Score Nodes
      │
      ▼
Best Node Selected
      │
      ▼
kubelet
      │
      ▼
Running Pod
```

---

# Scheduling Decision Tree

```text
Need a specific node?
        │
        ▼
nodeSelector

Need flexible node selection?
        │
        ▼
Node Affinity

Need Pods together?
        │
        ▼
Pod Affinity

Need Pods apart?
        │
        ▼
Pod Anti-Affinity

Need dedicated nodes?
        │
        ▼
Taints + Tolerations

Need critical Pods first?
        │
        ▼
PriorityClass
```

---

# nodeSelector

### Purpose

Schedule a Pod only on nodes with an exact matching label.

Example:

```yaml
nodeSelector:
  disktype: ssd
```

✔ Simple

✔ Exact match

❌ Not flexible

---

# Node Affinity

### Purpose

Advanced node selection using label expressions.

Types:

* requiredDuringSchedulingIgnoredDuringExecution
* preferredDuringSchedulingIgnoredDuringExecution

Example:

```yaml
requiredDuringSchedulingIgnoredDuringExecution
```

✔ Flexible

✔ Recommended for production

---

# Pod Affinity

### Purpose

Schedule Pods close to other Pods.

Examples:

* Web + Cache
* Frontend + Backend
* Low latency workloads

---

# Pod Anti-Affinity

### Purpose

Spread Pods across nodes.

Examples:

* HA applications
* Multi-zone deployments
* Prevent single node failure

---

# Taints

### Purpose

Repel Pods from nodes.

Example:

```bash
kubectl taint nodes worker-1 dedicated=db:NoSchedule
```

Effects:

| Effect           | Meaning                                          |
| ---------------- | ------------------------------------------------ |
| NoSchedule       | Do not schedule new Pods                         |
| PreferNoSchedule | Avoid scheduling if possible                     |
| NoExecute        | Evict existing Pods without matching tolerations |

---

# Tolerations

### Purpose

Allow Pods to run on tainted nodes.

Important:

✔ Tolerations do **not** force scheduling.

They only allow scheduling.

---

# PriorityClass

Higher priority Pods are scheduled first.

Example use cases:

* CoreDNS
* Monitoring
* Logging
* Critical system workloads

---

# Preemption

If a high-priority Pod cannot be scheduled:

↓

Scheduler evicts lower-priority Pods

↓

Resources become available

↓

High-priority Pod starts

---

# Comparison Table

| Feature           | nodeSelector | Node Affinity |
| ----------------- | ------------ | ------------- |
| Exact Match       | ✅            | ✅             |
| Expressions       | ❌            | ✅             |
| Soft Rules        | ❌            | ✅             |
| Production Choice | ⚠️ Limited   | ✅ Preferred   |

---

| Feature       | Pod Affinity | Pod Anti-Affinity |
| ------------- | ------------ | ----------------- |
| Pods Together | ✅            | ❌                 |
| Spread Pods   | ❌            | ✅                 |
| HA            | ❌            | ✅                 |
| Low Latency   | ✅            | ❌                 |

---

| Feature          | Taints     | Tolerations      |
| ---------------- | ---------- | ---------------- |
| Applied To       | Nodes      | Pods             |
| Purpose          | Repel Pods | Allow Scheduling |
| Force Scheduling | ❌          | ❌                |

---

# Production Examples

| Requirement                     | Feature                |
| ------------------------------- | ---------------------- |
| GPU workloads                   | Node Affinity          |
| Database nodes                  | Taints + Tolerations   |
| Monitoring agent                | DaemonSet + Toleration |
| Web replicas on different nodes | Pod Anti-Affinity      |
| Cache near application          | Pod Affinity           |
| Critical monitoring             | PriorityClass          |

---

# Common Commands

```bash
kubectl get nodes

kubectl get nodes --show-labels

kubectl describe node <node>

kubectl describe pod <pod>

kubectl get events --sort-by=.lastTimestamp

kubectl taint nodes worker-1 dedicated=db:NoSchedule

kubectl label node worker-1 disktype=ssd

kubectl get pods -o wide
```

---

# Memory Tricks

## nodeSelector

Simple label match.

---

## Node Affinity

Smart label selection.

---

## Pod Affinity

Pods together.

---

## Pod Anti-Affinity

Pods apart.

---

## Taints

Repel Pods.

---

## Tolerations

Allow scheduling.

---

## PriorityClass

Schedule first.

---

## Preemption

Evict lower-priority Pods.

---

# Exam Tips

✔ Pods stuck in **Pending**?

Check:

```bash
kubectl describe pod
```

Then:

```bash
kubectl get events --sort-by=.lastTimestamp
```

---

✔ Need flexible node scheduling?

Use:

```text
Node Affinity
```

---

✔ Need one Pod on every node?

Use:

```text
DaemonSet
```

---

✔ Need dedicated nodes?

Use:

```text
Taints + Tolerations
```

---

✔ Need highly available workloads?

Use:

```text
Pod Anti-Affinity
```

---

# Quick Review Checklist

☐ Scheduler Workflow

☐ nodeSelector

☐ Node Affinity

☐ Pod Affinity

☐ Pod Anti-Affinity

☐ Taints

☐ Tolerations

☐ PriorityClass

☐ Preemption

☐ Pending Pod Troubleshooting
