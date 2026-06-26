# Scheduling - Troubleshooting Guide

> A production-focused guide for diagnosing and resolving Kubernetes scheduling problems.

---

# Troubleshooting Workflow

Always investigate scheduling problems in the following order:

```text
Pod Pending
      │
      ▼
kubectl describe pod
      │
      ▼
Events
      │
      ▼
Node Labels
      │
      ▼
Affinity Rules
      │
      ▼
Taints
      │
      ▼
Resources
      │
      ▼
Scheduler Decision
      │
      ▼
Root Cause
```

Never start by editing YAML without understanding why scheduling failed.

---

# Scenario 1 - Pod Stuck in Pending

## Symptoms

```text
STATUS

Pending
```

---

## Investigation

```bash
kubectl get pods

kubectl describe pod <pod-name>

kubectl get events --sort-by=.lastTimestamp

kubectl get nodes
```

---

## Possible Causes

* No eligible nodes
* Resource shortage
* Missing node label
* Taints
* PVC not bound
* Node NotReady

---

## Resolution

Identify the scheduling restriction and correct it.

---

## Prevention

Always check node labels and resource availability before deploying workloads.

---

# Scenario 2 - nodeSelector Does Not Match Any Node

## Symptoms

Pod remains Pending.

---

## Investigation

```bash
kubectl get nodes --show-labels

kubectl get pod <pod> -o yaml
```

Compare:

```yaml
nodeSelector:
  disktype: ssd
```

with actual node labels.

---

## Resolution

Either:

* Add the required label

or

* Update the Pod specification.

---

# Scenario 3 - Node Affinity Prevents Scheduling

## Symptoms

Pod cannot be scheduled.

---

## Investigation

```bash
kubectl describe pod <pod>

kubectl get pod <pod> -o yaml

kubectl get nodes --show-labels
```

Verify:

* required rules
* preferred rules
* label expressions

---

## Possible Causes

* Required rule impossible to satisfy
* Incorrect label key
* Incorrect operator

---

# Scenario 4 - Pod Affinity Conflict

## Symptoms

Scheduler cannot satisfy affinity requirements.

---

## Investigation

```bash
kubectl describe pod <pod>

kubectl get pods --show-labels

kubectl get pods -o wide
```

---

## Resolution

Verify that the referenced Pods exist and have the expected labels.

---

# Scenario 5 - Pod Anti-Affinity Conflict

## Symptoms

Large replica count but insufficient nodes.

Example:

```text
Replicas: 5

Nodes: 2
```

---

## Investigation

```bash
kubectl describe pod <pod>

kubectl get nodes

kubectl get pods -o wide
```

---

## Root Cause

Strict Anti-Affinity requires more nodes than are available.

---

## Resolution

Either:

* Add more nodes.

or

* Relax the Anti-Affinity rule.

---

# Scenario 6 - Taint Prevents Scheduling

## Symptoms

Pod remains Pending.

Events show:

```text
had taint
```

---

## Investigation

```bash
kubectl describe node <node>

kubectl describe pod <pod>
```

---

## Resolution

Either:

* Remove the taint.

or

* Add a matching toleration.

---

# Scenario 7 - NoExecute Evicts Running Pods

## Symptoms

Pods disappear after a taint is added.

---

## Investigation

```bash
kubectl describe node

kubectl get events
```

---

## Root Cause

The Pod does not tolerate a `NoExecute` taint.

---

## Resolution

Add an appropriate toleration or remove the taint.

---

# Scenario 8 - Insufficient CPU or Memory

## Symptoms

Pod Pending.

Events show:

```text
Insufficient cpu

or

Insufficient memory
```

---

## Investigation

```bash
kubectl describe pod

kubectl describe node
```

Review:

* Requests
* Limits
* Allocatable resources

---

## Resolution

* Reduce resource requests.
* Scale the cluster.
* Free resources.

---

# Scenario 9 - Node NotReady

## Symptoms

Scheduler ignores a node.

---

## Investigation

```bash
kubectl get nodes

kubectl describe node <node>
```

Expected:

```text
STATUS

NotReady
```

---

## Resolution

Restore node health before expecting new workloads to be scheduled.

---

# Scenario 10 - Priority Pod Cannot Schedule

## Symptoms

High-priority Pod still Pending.

---

## Investigation

```bash
kubectl describe pod

kubectl get priorityclass

kubectl get events
```

---

## Possible Causes

* No preemption candidates
* Resources still unavailable
* Scheduling constraints

---

# Production Debugging Checklist

Always verify:

## Pod

```bash
kubectl describe pod <pod>
```

---

## Events

```bash
kubectl get events --sort-by=.lastTimestamp
```

---

## Nodes

```bash
kubectl get nodes

kubectl describe node <node>
```

---

## Labels

```bash
kubectl get nodes --show-labels
```

---

## Affinity

```bash
kubectl get pod <pod> -o yaml
```

---

## Taints

```bash
kubectl describe node <node>
```

---

## Resources

```bash
kubectl describe node <node>
```

---

# Common Scheduling Events

| Event                              | Meaning                           |
| ---------------------------------- | --------------------------------- |
| FailedScheduling                   | Scheduler could not place the Pod |
| Insufficient cpu                   | CPU unavailable                   |
| Insufficient memory                | Memory unavailable                |
| NodeAffinity                       | Affinity rules failed             |
| node(s) had taint                  | Missing toleration                |
| node(s) didn't match node selector | Label mismatch                    |

---

# Production Tips

* Always read Events before modifying YAML.
* Verify labels before troubleshooting nodeSelector.
* Keep affinity rules as simple as possible.
* Avoid unnecessary scheduling constraints.
* Reserve dedicated nodes with Taints and Tolerations.
* Monitor cluster capacity before increasing replicas.
* Document recurring scheduling issues and their resolutions.

---

# Interviewer's Perspective

A common senior interview question is:

> "A Pod has been stuck in Pending for 30 minutes. Walk me through your troubleshooting process."

A strong answer includes:

1. Describe the Pod.
2. Review Events.
3. Verify node health.
4. Check labels.
5. Check nodeSelector and affinity.
6. Verify taints and tolerations.
7. Check CPU and memory availability.
8. Review PriorityClass and preemption if applicable.
9. Identify the root cause.
10. Apply the minimum necessary fix and verify the Pod schedules successfully.

This structured approach demonstrates production-ready troubleshooting skills rather than simply recalling Kubernetes commands.
