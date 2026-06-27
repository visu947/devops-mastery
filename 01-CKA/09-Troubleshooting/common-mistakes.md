# Troubleshooting - Common Mistakes

> **Learn the most common Kubernetes troubleshooting mistakes, why they happen, and how to avoid them during the CKA exam and in production.**

---

# Why Troubleshooting Fails

Most Kubernetes troubleshooting failures are **not caused by lack of knowledge**.

They are caused by:

* Guessing instead of investigating
* Skipping important diagnostic steps
* Changing multiple things at once
* Ignoring Events and Logs
* Not verifying the fix

Following a structured process prevents these mistakes.

---

# 1. Restarting Pods Before Investigating

## Mistake

Immediately deleting or restarting a failing Pod.

```bash
kubectl delete pod <pod-name>
```

## Why It's a Problem

* Valuable logs may be lost.
* The root cause remains unresolved.
* The issue may immediately return.

## Correct Approach

First collect evidence:

```bash
kubectl describe pod <pod-name>

kubectl logs <pod-name>

kubectl logs <pod-name> --previous
```

Only restart the Pod after understanding the failure.

---

# 2. Ignoring Events

## Mistake

Looking only at Pod status.

## Why It's a Problem

Most scheduling and deployment failures are explained in Events.

Examples:

* FailedScheduling
* FailedMount
* FailedAttachVolume
* FailedCreate
* FailedPull

## Correct Approach

Always check:

```bash
kubectl get events --sort-by=.lastTimestamp
```

or

```bash
kubectl describe pod <pod-name>
```

---

# 3. Reading Logs Before Describing the Resource

## Mistake

Jumping directly to:

```bash
kubectl logs <pod-name>
```

## Why It's a Problem

Some failures occur before the container even starts.

Examples:

* ImagePullBackOff
* Pending
* FailedScheduling
* FailedMount

Logs may not exist.

## Correct Approach

Use this order:

1. `kubectl get`
2. `kubectl describe`
3. `kubectl logs`

---

# 4. Forgetting Previous Logs

## Mistake

Using only:

```bash
kubectl logs <pod-name>
```

## Why It's a Problem

CrashLoopBackOff often restarts containers before you can inspect them.

## Correct Approach

Use:

```bash
kubectl logs <pod-name> --previous
```

to view the logs from the previous container instance.

---

# 5. Ignoring Labels and Selectors

## Mistake

Assuming a Service automatically discovers Pods.

## Why It's a Problem

A Service routes traffic only to Pods matching its selector.

If labels don't match:

* Endpoints remain empty.
* The Service appears broken.

## Correct Approach

Verify:

```bash
kubectl get svc

kubectl describe svc <service-name>

kubectl get endpoints

kubectl get pods --show-labels
```

---

# 6. Skipping Dependency Checks

## Mistake

Debugging only the affected resource.

## Why It's a Problem

Many Kubernetes resources depend on others.

Examples:

* Deployment → ReplicaSet → Pod
* Service → Endpoint → Pod
* PVC → PV → StorageClass
* Pod → Secret → ConfigMap

## Correct Approach

Always check dependent resources.

---

# 7. Changing Multiple Things at Once

## Mistake

Editing several configuration values before testing.

## Why It's a Problem

You won't know which change fixed—or introduced—the problem.

## Correct Approach

Apply one change.

Verify.

Repeat if necessary.

---

# 8. Ignoring Node Health

## Mistake

Assuming the application is at fault.

## Why It's a Problem

The node itself may have:

* MemoryPressure
* DiskPressure
* kubelet failure
* Runtime failure

## Correct Approach

Check:

```bash
kubectl get nodes

kubectl describe node <node-name>
```

---

# 9. Ignoring Resource Requests and Limits

## Mistake

Focusing only on the application.

## Why It's a Problem

Pods may remain Pending due to insufficient CPU or memory.

## Correct Approach

Review:

* Resource requests
* Resource limits
* Node capacity

---

# 10. Forgetting to Verify the Fix

## Mistake

Stopping after making a change.

## Why It's a Problem

The underlying issue may still exist.

## Correct Approach

Always verify:

```bash
kubectl get pods

kubectl get events

kubectl get nodes
```

Confirm the application behaves as expected.

---

# 11. Ignoring kube-system

## Mistake

Checking only application namespaces.

## Why It's a Problem

Core components may be unhealthy.

Examples:

* CoreDNS
* kube-proxy
* API Server
* etcd

## Correct Approach

Always verify:

```bash
kubectl get pods -n kube-system
```

---

# 12. Assuming It's a Kubernetes Problem

## Mistake

Believing Kubernetes is always the cause.

## Why It's a Problem

Many incidents originate from:

* Application bugs
* Database failures
* External APIs
* DNS outside the cluster
* Cloud infrastructure

## Correct Approach

Determine whether the issue is:

* Kubernetes
* Application
* Infrastructure
* External dependency

---

# Common Troubleshooting Mistakes

| Mistake             | Better Practice                             |
| ------------------- | ------------------------------------------- |
| Restart first       | Collect evidence first                      |
| Ignore Events       | Review Events early                         |
| Read logs first     | Describe before logs                        |
| Skip previous logs  | Use `--previous`                            |
| Ignore labels       | Verify selectors and Endpoints              |
| Ignore dependencies | Check related resources                     |
| Change many things  | Change one thing at a time                  |
| Ignore node health  | Verify node conditions                      |
| Ignore resources    | Check requests and limits                   |
| Skip verification   | Confirm the fix                             |
| Ignore kube-system  | Verify control plane health                 |
| Blame Kubernetes    | Consider application and infrastructure too |

---

# Production Best Practices

* Start with the simplest explanation.
* Gather evidence before making changes.
* Read Events before editing manifests.
* Check Logs after describing the resource.
* Follow dependencies through the Kubernetes object chain.
* Make one change at a time.
* Verify the solution before closing the incident.
* Document the root cause and resolution.

---

# CKA Exam Tips

During the exam:

* Don't panic.
* Read the task carefully.
* Start with `kubectl get`.
* Use `kubectl describe` to inspect Events.
* Use `kubectl logs` for application failures.
* Verify the fix before moving to the next question.

---

# Golden Rule

The best Kubernetes engineers don't troubleshoot by intuition.

They troubleshoot using evidence.

A simple process to remember:

```text
Observe

↓

Describe

↓

Events

↓

Logs

↓

Dependencies

↓

Root Cause

↓

Fix

↓

Verify
```

Following this workflow consistently will help you solve problems faster, avoid introducing new issues, and perform well in both production environments and the CKA exam.
