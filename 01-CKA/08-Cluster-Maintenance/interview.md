# Cluster Maintenance - Interview Questions & Answers

> **Frequently asked Kubernetes Cluster Maintenance interview questions for the CKA exam and Senior DevOps interviews.**

---

# Beginner Level

## 1. What is Cluster Maintenance?

### Answer

Cluster Maintenance refers to the operational tasks required to keep a Kubernetes cluster healthy, secure, and up to date.

Typical activities include:

* Node maintenance
* Cluster upgrades
* etcd backup and restore
* Certificate renewal
* Health verification
* Applying security patches

---

## 2. What is the difference between `cordon`, `drain`, and `uncordon`?

### Answer

| Command    | Purpose                                                        |
| ---------- | -------------------------------------------------------------- |
| `cordon`   | Marks a node as unschedulable. Existing Pods continue running. |
| `drain`    | Safely evicts workloads before maintenance.                    |
| `uncordon` | Marks the node schedulable again.                              |

---

## 3. Why do we drain a node?

### Answer

Draining safely evicts application Pods before maintenance or upgrades.

Benefits:

* Prevents application disruption.
* Allows workloads to move to healthy nodes.
* Ensures a controlled maintenance process.

---

## 4. What is etcd?

### Answer

etcd is Kubernetes' distributed key-value datastore.

It stores the entire cluster state, including:

* Pods
* Deployments
* Services
* ConfigMaps
* Secrets
* RBAC
* PersistentVolumeClaims
* Cluster configuration

If etcd is unavailable, the control plane cannot manage cluster state.

---

## 5. Why is etcd backup important?

### Answer

An etcd backup allows the cluster state to be restored after accidental deletion, corruption, or infrastructure failure.

Without a recent backup, recovery may not be possible.

---

# Intermediate Level

## 6. What is the recommended Kubernetes upgrade order?

### Answer

```text id="1qqm0e"
Backup etcd

↓

Upgrade Control Plane

↓

Verify Cluster

↓

Drain Worker Node

↓

Upgrade Worker Node

↓

Restart kubelet

↓

Uncordon Worker

↓

Repeat
```

The control plane should always be upgraded before worker nodes.

---

## 7. What is Version Skew?

### Answer

Version Skew defines the supported version differences between Kubernetes components.

Examples:

* `kubectl` should be within one minor version of the API server.
* kubelets should remain within the supported version skew of the control plane.

Following the Version Skew Policy helps maintain compatibility during upgrades.

---

## 8. Why shouldn't worker nodes be upgraded first?

### Answer

Worker nodes depend on the control plane.

Upgrading workers before the control plane may introduce unsupported version combinations and operational issues.

---

## 9. What happens during a node drain?

### Answer

During a drain:

* The node is marked unschedulable.
* Workload Pods are evicted.
* DaemonSet Pods remain by default.
* Pods managed by controllers are rescheduled to other nodes.

---

## 10. Why doesn't `kubectl drain` remove DaemonSet Pods?

### Answer

DaemonSets are designed to run on every eligible node.

Removing them during a drain could interfere with critical services such as networking, logging, or monitoring agents.

---

# Advanced Level

## 11. How would you safely upgrade a production Kubernetes cluster?

### Answer

A typical process is:

1. Review release notes.
2. Back up etcd.
3. Check the upgrade plan with `kubeadm`.
4. Upgrade the control plane.
5. Verify cluster health.
6. Drain one worker node.
7. Upgrade the worker.
8. Restart kubelet if required.
9. Uncordon the node.
10. Repeat for remaining worker nodes.

---

## 12. How do you verify cluster health after maintenance?

### Answer

Common checks include:

```bash id="r0pydy"
kubectl get nodes

kubectl get pods -A

kubectl get pods -n kube-system

kubectl cluster-info

kubectl get events --sort-by=.lastTimestamp
```

Verify:

* Nodes are `Ready`.
* System Pods are healthy.
* Workloads are running.
* No critical events are reported.

---

## 13. How do you check certificate expiration?

### Answer

```bash id="r7a7vg"
kubeadm certs check-expiration
```

This displays the remaining validity period for Kubernetes certificates.

---

## 14. What happens if Kubernetes certificates expire?

### Answer

Expired certificates can prevent secure communication between components.

Possible symptoms include:

* API server connectivity failures.
* kubelet authentication failures.
* `kubectl` connection errors.

Certificates should be renewed before expiration.

---

## 15. How do you renew certificates?

### Answer

Renew all certificates:

```bash id="f7vqkz"
kubeadm certs renew all
```

Or renew individual certificates as required.

---

# Production Scenarios

## 16. A node is in `Ready,SchedulingDisabled`. What does it mean?

### Answer

The node has been cordoned.

* Existing Pods continue running.
* New Pods are not scheduled.

Use:

```bash id="6v3wzq"
kubectl uncordon <node-name>
```

to return the node to normal scheduling.

---

## 17. A node remains unschedulable after maintenance. What do you check?

### Answer

Verify:

* The node has been uncordoned.
* Node status is `Ready`.
* kubelet is running.
* No taints or scheduling restrictions prevent Pod placement.

---

## 18. How would you recover from an etcd failure?

### Answer

Typical recovery steps:

1. Stop the affected etcd instance.
2. Restore from a verified snapshot.
3. Configure etcd to use the restored data.
4. Restart etcd.
5. Verify API server functionality.
6. Confirm cluster resources are present.

---

## 19. An upgrade completed, but workloads are failing. What do you check?

### Answer

Review:

* Node readiness.
* kube-system Pods.
* Events.
* Application logs.
* Version compatibility.
* kubelet status.

Always verify each maintenance step before proceeding to the next node.

---

## 20. How would you minimize downtime during maintenance?

### Answer

Best practices:

* Upgrade one node at a time.
* Use `drain` before maintenance.
* Ensure workloads have replicas.
* Respect PodDisruptionBudgets.
* Verify health after each step.
* Keep rollback procedures available.

---

# Rapid Fire

### What does `cordon` do?

Marks a node unschedulable.

---

### What does `drain` do?

Safely evicts workloads from a node.

---

### What does `uncordon` do?

Allows the scheduler to place new Pods on the node again.

---

### Where is Kubernetes cluster state stored?

**etcd**

---

### What command checks certificate expiration?

```bash id="v5crgi"
kubeadm certs check-expiration
```

---

### Which component should be upgraded first?

**Control Plane**

---

### What is Version Skew?

The supported version difference between Kubernetes components.

---

### Why should etcd be backed up before upgrades?

To enable recovery if the upgrade fails or cluster state is lost.

---

# Interview Tips

When answering cluster maintenance questions:

* Describe the overall maintenance workflow.
* Mention backups before upgrades.
* Explain why the control plane is upgraded first.
* Highlight verification after each maintenance step.
* Emphasize minimizing downtime and following the Version Skew Policy.

Interviewers value candidates who demonstrate safe operational procedures, not just command memorization.
