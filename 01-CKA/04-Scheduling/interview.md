# Scheduling - Interview Questions

> This document contains Kubernetes Scheduling interview questions ranging from beginner to senior DevOps engineer level.

---

# 🟢 Beginner Level

---

# 1. What is the Kubernetes Scheduler?

## Short Answer

The Kubernetes Scheduler is the control plane component responsible for assigning Pods to suitable nodes based on resource availability and scheduling constraints.

### Detailed Answer

When a Pod is created without an assigned node, the Scheduler:

1. Watches for unscheduled Pods.
2. Finds eligible nodes.
3. Filters nodes that don't meet scheduling requirements.
4. Scores the remaining nodes.
5. Assigns the Pod to the best node.

The kubelet on that node then starts the Pod.

### Production Insight

Without the Scheduler, Pods remain in the **Pending** state because no node is assigned.

---

# 2. What happens when a Pod is created?

## Short Answer

The Pod is stored in the API Server, the Scheduler selects a node, and the kubelet on that node creates the Pod.

### Workflow

```text
kubectl apply
        ↓
API Server
        ↓
Scheduler
        ↓
Node Selected
        ↓
kubelet
        ↓
Container Runtime
        ↓
Running Pod
```

---

# 3. What is nodeSelector?

## Short Answer

`nodeSelector` schedules a Pod only on nodes with matching labels.

### Example

```yaml
nodeSelector:
  disktype: ssd
```

### Production Insight

Simple and effective, but limited to exact label matches.

---

# 4. What is Node Affinity?

## Short Answer

Node Affinity provides flexible scheduling using label expressions instead of exact matches.

### Types

* requiredDuringSchedulingIgnoredDuringExecution
* preferredDuringSchedulingIgnoredDuringExecution

### Production Insight

Node Affinity is preferred over nodeSelector for production workloads.

---

# 5. What is the difference between nodeSelector and Node Affinity?

| Feature         | nodeSelector | Node Affinity |
| --------------- | ------------ | ------------- |
| Exact Match     | ✅            | ✅             |
| Expressions     | ❌            | ✅             |
| Preferred Rules | ❌            | ✅             |
| Flexibility     | Low          | High          |

---

# 🟡 Intermediate Level

---

# 6. What is Pod Affinity?

## Short Answer

Pod Affinity schedules Pods close to other Pods.

### Examples

* Web + Cache
* Frontend + Backend
* Latency-sensitive applications

---

# 7. What is Pod Anti-Affinity?

## Short Answer

Pod Anti-Affinity spreads Pods across different nodes to improve availability.

### Production Example

Three replicas of an API are placed on three different nodes so that a single node failure does not take down the entire application.

---

# 8. What are Taints?

## Short Answer

Taints repel Pods from nodes.

Only Pods with matching tolerations can be scheduled onto tainted nodes.

### Example

```bash
kubectl taint nodes worker-1 dedicated=db:NoSchedule
```

---

# 9. What are Tolerations?

## Short Answer

A Toleration allows a Pod to be scheduled onto a tainted node.

### Important

A toleration **does not force** scheduling to that node.

It simply removes the scheduling restriction imposed by the taint.

---

# 10. Explain the three taint effects.

| Effect           | Description                                        |
| ---------------- | -------------------------------------------------- |
| NoSchedule       | Prevent new Pods from being scheduled.             |
| PreferNoSchedule | Avoid scheduling if possible.                      |
| NoExecute        | Evict running Pods that do not tolerate the taint. |

---

# 🔴 Senior Level

---

# 11. How does the Scheduler choose a node?

## Detailed Answer

The Scheduler performs two major phases:

1. **Filtering** – removes nodes that cannot run the Pod.
2. **Scoring** – ranks the remaining nodes based on scheduling policies.

The node with the highest score is selected.

---

# 12. Explain required vs preferred Node Affinity.

| Type                                            | Behavior         |
| ----------------------------------------------- | ---------------- |
| requiredDuringSchedulingIgnoredDuringExecution  | Mandatory rule.  |
| preferredDuringSchedulingIgnoredDuringExecution | Soft preference. |

### Interview Tip

Use **required** when a Pod must run on certain nodes and **preferred** when you'd like it to, but can accept other nodes if necessary.

---

# 13. When would you use Pod Affinity instead of Node Affinity?

## Answer

Use Pod Affinity when placement depends on **other Pods**, not node labels.

Example:

* Keep an application close to its Redis cache to reduce network latency.

---

# 14. Why are Taints and Tolerations used together?

## Answer

Taints protect nodes from unwanted workloads.

Tolerations allow specific workloads to run on those protected nodes.

Example:

* Reserve GPU nodes for ML workloads.
* Reserve database nodes for database Pods.
* Reserve infrastructure nodes for monitoring agents.

---

# 15. What is PriorityClass?

## Short Answer

PriorityClass assigns scheduling priority to Pods.

Higher-priority Pods are scheduled before lower-priority Pods when resources are limited.

---

# 16. What is Preemption?

## Short Answer

If a high-priority Pod cannot be scheduled, Kubernetes may evict lower-priority Pods to free resources.

### Production Insight

Preemption is used only when necessary and only if it enables scheduling of a higher-priority Pod.

---

# 🏢 Production Scenarios

---

# 17. A Pod is stuck in Pending. How do you troubleshoot it?

### Suggested Workflow

```bash
kubectl get pods

kubectl describe pod <pod>

kubectl get events --sort-by=.lastTimestamp

kubectl get nodes

kubectl describe node <node>
```

### Common Causes

* Insufficient CPU
* Insufficient memory
* Missing node label
* Taints
* PVC not bound
* Node NotReady

---

# 18. How would you dedicate nodes for databases?

### Recommended Approach

1. Label the database nodes.
2. Apply a taint to prevent other workloads.
3. Configure database Pods with:

   * Node Affinity
   * Matching Toleration

This ensures only database workloads run on those nodes.

---

# 19. How would you spread replicas across nodes?

### Answer

Use Pod Anti-Affinity.

This minimizes the impact of a node failure by avoiding co-locating replicas on the same node.

---

# 20. Your application requires GPU nodes. How would you schedule it?

### Answer

* Label GPU nodes.
* Use Node Affinity or nodeSelector.
* Optionally taint GPU nodes.
* Add matching tolerations to GPU workloads.

---

# Common Interview Follow-up Questions

* Why is nodeSelector considered limited?
* Why doesn't a toleration guarantee scheduling?
* What is the difference between Affinity and Anti-Affinity?
* Why are Pods still Pending even though nodes exist?
* What is the Scheduler's filtering phase?
* What is the Scheduler's scoring phase?
* How does Preemption work?
* Why are PriorityClasses important?
* Can a DaemonSet run on tainted nodes?
* What happens if a node label changes after a Pod is scheduled?

---

# Interviewer's Perspective

Senior interviewers rarely stop at definitions.

Instead of asking:

> "What is Node Affinity?"

They often ask:

* Why choose Node Affinity instead of nodeSelector?
* How would you isolate database workloads?
* Why are Pods Pending even when there are free nodes?
* How would you schedule GPU workloads?
* Explain how the Scheduler filters and scores nodes.
* How would you design a highly available deployment across multiple nodes?

The strongest candidates explain not only **what** each scheduling feature does, but also **why** they would choose it in a production environment.
