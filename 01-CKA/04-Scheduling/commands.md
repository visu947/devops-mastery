# Scheduling - Commands

> **Quick reference for Kubernetes Scheduling commands used in the CKA exam, production environments, and technical interviews.**

---

# Table of Contents

1. Cluster Information
2. Node Labels
3. nodeSelector
4. Node Affinity
5. Pod Affinity
6. Pod Anti-Affinity
7. Taints
8. Tolerations
9. Priority Classes
10. Scheduler Debugging
11. Pending Pods
12. Events
13. Useful Output Formats
14. Explain Resources
15. Production Tips

---

# 1. Cluster Information

## List Nodes

```bash
kubectl get nodes
```

Short form:

```bash
kubectl get no
```

---

## Detailed Node Information

```bash
kubectl describe node <node-name>
```

---

## Show Node Labels

```bash
kubectl get nodes --show-labels
```

---

## Wide Output

```bash
kubectl get nodes -o wide
```

---

# 2. Node Labels

## Add Label

```bash
kubectl label node worker-1 disktype=ssd
```

---

## Overwrite Existing Label

```bash
kubectl label node worker-1 disktype=ssd --overwrite
```

---

## Remove Label

```bash
kubectl label node worker-1 disktype-
```

---

## Verify Labels

```bash
kubectl get nodes --show-labels
```

---

# 3. nodeSelector

## View Pod

```bash
kubectl get pod <pod-name> -o yaml
```

---

## Describe Pod

```bash
kubectl describe pod <pod-name>
```

---

## Verify Assigned Node

```bash
kubectl get pod -o wide
```

---

# 4. Node Affinity

## View Affinity Rules

```bash
kubectl get pod <pod-name> -o yaml
```

---

## Explain Affinity

```bash
kubectl explain pod.spec.affinity
```

---

## Explain Node Affinity

```bash
kubectl explain pod.spec.affinity.nodeAffinity
```

---

# 5. Pod Affinity

## View Pod Affinity

```bash
kubectl get pod <pod-name> -o yaml
```

---

## Describe Pod

```bash
kubectl describe pod <pod-name>
```

---

## Explain Pod Affinity

```bash
kubectl explain pod.spec.affinity.podAffinity
```

---

# 6. Pod Anti-Affinity

## Explain Anti-Affinity

```bash
kubectl explain pod.spec.affinity.podAntiAffinity
```

---

## Verify Pod Placement

```bash
kubectl get pods -o wide
```

---

# 7. Taints

## View Node Taints

```bash
kubectl describe node <node-name>
```

---

## Add NoSchedule Taint

```bash
kubectl taint nodes worker-1 dedicated=db:NoSchedule
```

---

## Add PreferNoSchedule

```bash
kubectl taint nodes worker-1 dedicated=db:PreferNoSchedule
```

---

## Add NoExecute

```bash
kubectl taint nodes worker-1 dedicated=db:NoExecute
```

---

## Remove Taint

```bash
kubectl taint nodes worker-1 dedicated=db:NoSchedule-
```

---

# 8. Tolerations

## View Tolerations

```bash
kubectl describe pod <pod-name>
```

---

## View YAML

```bash
kubectl get pod <pod-name> -o yaml
```

---

## Explain Tolerations

```bash
kubectl explain pod.spec.tolerations
```

---

# 9. Priority Classes

## List Priority Classes

```bash
kubectl get priorityclass
```

---

## Describe Priority Class

```bash
kubectl describe priorityclass <name>
```

---

## View YAML

```bash
kubectl get priorityclass -o yaml
```

---

# 10. Scheduler Debugging

## Show Pending Pods

```bash
kubectl get pods --field-selector=status.phase=Pending
```

---

## Describe Pending Pod

```bash
kubectl describe pod <pod-name>
```

---

## View Scheduling Events

```bash
kubectl get events --sort-by=.lastTimestamp
```

---

## Scheduler Logs (Self-Managed Clusters)

```bash
kubectl logs -n kube-system kube-scheduler-<control-plane-node>
```

---

# 11. Pending Pods

## List Pending Pods

```bash
kubectl get pods --field-selector=status.phase=Pending
```

---

## Watch Pending Pods

```bash
kubectl get pods -w
```

---

## Verify Assigned Node

```bash
kubectl get pods -o wide
```

---

# 12. Events

## List Events

```bash
kubectl get events
```

---

## Sort by Time

```bash
kubectl get events --sort-by=.lastTimestamp
```

---

## Namespace Events

```bash
kubectl get events -n <namespace>
```

---

# 13. Useful Output Formats

## Pod YAML

```bash
kubectl get pod <pod-name> -o yaml
```

---

## Node YAML

```bash
kubectl get node <node-name> -o yaml
```

---

## PriorityClass YAML

```bash
kubectl get priorityclass -o yaml
```

---

# 14. Explain Resources

## nodeSelector

```bash
kubectl explain pod.spec.nodeSelector
```

---

## Affinity

```bash
kubectl explain pod.spec.affinity
```

---

## Node Affinity

```bash
kubectl explain pod.spec.affinity.nodeAffinity
```

---

## Pod Affinity

```bash
kubectl explain pod.spec.affinity.podAffinity
```

---

## Pod Anti-Affinity

```bash
kubectl explain pod.spec.affinity.podAntiAffinity
```

---

## Tolerations

```bash
kubectl explain pod.spec.tolerations
```

---

## PriorityClass

```bash
kubectl explain priorityclass
```

---

# 15. Most Common Scheduling Troubleshooting Commands

```bash
kubectl get nodes

kubectl get pods -o wide

kubectl get pods --field-selector=status.phase=Pending

kubectl describe pod <pod-name>

kubectl describe node <node-name>

kubectl get events --sort-by=.lastTimestamp

kubectl get nodes --show-labels

kubectl get pod <pod-name> -o yaml

kubectl explain pod.spec.affinity

kubectl explain pod.spec.tolerations
```

---

# Production Tips

✅ Check Events before changing manifests.

✅ Verify node labels before using nodeSelector.

✅ Prefer Node Affinity over nodeSelector for flexible scheduling.

✅ Remember:

* nodeSelector → Exact label match
* Node Affinity → Flexible rules
* Pod Affinity → Pods together
* Pod Anti-Affinity → Pods apart
* Taints → Repel Pods
* Tolerations → Allow scheduling
* PriorityClass → Higher scheduling priority

---

# CKA Exam Tips

✔ `kubectl describe pod` is usually the fastest way to identify why a Pod is Pending.

✔ `kubectl get events --sort-by=.lastTimestamp` often reveals scheduling failures immediately.

✔ Use `kubectl explain` if you forget the YAML structure during the exam.

✔ Always verify node labels before troubleshooting nodeSelector or affinity issues.

---

# References

* Kubernetes Scheduler Documentation
* Assigning Pods to Nodes
* Node Affinity Documentation
* Taints and Tolerations Documentation
* Priority and Preemption Documentation
