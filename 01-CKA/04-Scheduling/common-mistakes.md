# Scheduling - Common Mistakes

> This document highlights common scheduling mistakes seen in the CKA exam, production environments, and Kubernetes interviews.

---

# Mistake 1

## Confusing nodeSelector with Node Affinity

❌ Incorrect

Many engineers think they are identical.

### Reality

* nodeSelector performs an exact label match.
* Node Affinity supports advanced expressions and preferred rules.

### Recommendation

Use Node Affinity for production workloads unless a simple exact match is sufficient.

---

# Mistake 2

## Forgetting to Label Nodes

Example:

```yaml
nodeSelector:
  disktype: ssd
```

If no node has the label:

```text
disktype=ssd
```

The Pod remains:

```text
Pending
```

### Verify

```bash
kubectl get nodes --show-labels
```

---

# Mistake 3

## Assuming Tolerations Force Scheduling

❌ Incorrect

Adding a toleration does **not** make Kubernetes schedule a Pod onto a tainted node.

### Reality

A toleration only allows scheduling.

The Scheduler still considers:

* Resources
* Affinity
* Other scheduling rules
* Node health

---

# Mistake 4

## Using nodeSelector for Complex Scheduling

Example:

Trying to schedule Pods based on:

* Multiple labels
* Preferred locations
* Expressions

### Better Solution

Use Node Affinity.

---

# Mistake 5

## Confusing Pod Affinity with Node Affinity

### Node Affinity

Schedules Pods based on **node labels**.

### Pod Affinity

Schedules Pods based on **other Pods**.

---

# Mistake 6

## Forgetting Pod Anti-Affinity for High Availability

Without Anti-Affinity:

```text
Node 1

API-1

API-2

API-3
```

Node failure:

All replicas are lost.

### Better

```text
Node1 → API-1

Node2 → API-2

Node3 → API-3
```

---

# Mistake 7

## Tainting Nodes Without Adding Tolerations

Result:

Pods remain:

```text
Pending
```

Always verify:

```bash
kubectl describe pod
```

---

# Mistake 8

## Ignoring Events

Many engineers immediately edit YAML.

Instead, check:

```bash
kubectl get events --sort-by=.lastTimestamp
```

Events often identify the scheduling problem immediately.

---

# Mistake 9

## Ignoring Resource Requests

A node may appear healthy, but if the requested CPU or memory is unavailable:

The Pod remains:

```text
Pending
```

Always verify:

```bash
kubectl describe pod
```

---

# Mistake 10

## Assuming Preemption Always Happens

❌ Incorrect

Preemption occurs only when:

* Higher-priority Pods exist.
* Lower-priority Pods can be evicted.
* Preemption would allow scheduling.

---

# Mistake 11

## Applying Too Many Scheduling Rules

Example:

* nodeSelector
* Node Affinity
* Pod Affinity
* Pod Anti-Affinity
* Taints
* Tolerations

Combined incorrectly:

No eligible node exists.

The Pod stays Pending.

---

# Mistake 12

## Forgetting That Scheduling Happens Only Once

Many engineers assume changing node labels automatically moves running Pods.

### Reality

Scheduling occurs only when the Pod is first scheduled.

Changing labels later does **not** automatically move the Pod.

---

# Mistake 13

## Assuming All Nodes Are Eligible

The Scheduler filters nodes using:

* Resources
* Labels
* Affinity
* Taints
* Node conditions

Only eligible nodes are scored.

---

# Mistake 14

## Misunderstanding NoExecute

Many think:

NoExecute = NoSchedule

### Reality

NoExecute also evicts existing Pods that do not tolerate the taint.

---

# Mistake 15

## Using PriorityClass for Every Application

PriorityClasses should be reserved for truly important workloads.

Examples:

* CoreDNS
* Monitoring
* Logging
* Ingress Controllers
* Critical platform services

Using them for every workload reduces their effectiveness.

---

# Quick Comparison

| Incorrect Assumption           | Correct Understanding                                          |
| ------------------------------ | -------------------------------------------------------------- |
| nodeSelector = Node Affinity   | Node Affinity is more flexible                                 |
| Toleration forces scheduling   | It only permits scheduling                                     |
| Affinity uses node labels only | Pod Affinity uses other Pods                                   |
| Preemption always occurs       | Only under specific conditions                                 |
| Events are optional            | Events are often the fastest way to identify scheduling issues |

---

# Production Checklist

Before modifying scheduling rules, always verify:

* Node labels
* Resource availability
* Affinity rules
* Taints
* Tolerations
* Events
* Pod status
* Node health

---

# Memory Tips

✔ nodeSelector → Exact label match

✔ Node Affinity → Flexible scheduling

✔ Pod Affinity → Pods together

✔ Pod Anti-Affinity → Pods apart

✔ Taints → Repel Pods

✔ Tolerations → Allow scheduling

✔ PriorityClass → Higher scheduling priority

✔ Preemption → Evict lower-priority Pods if necessary

---

# Final Advice

Most scheduling problems are not caused by Kubernetes.

They are caused by:

* Missing labels
* Incorrect affinity rules
* Missing tolerations
* Resource shortages
* Misunderstanding how the Scheduler makes placement decisions

Always troubleshoot methodically before changing manifests.
