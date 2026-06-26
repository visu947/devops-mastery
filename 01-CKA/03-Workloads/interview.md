# Workloads - Interview Questions

> This document contains Kubernetes Workloads interview questions ranging from beginner to senior DevOps engineer level.

---

# 🟢 Beginner Level

---

# 1. What is a Kubernetes Workload?

## Short Answer

A workload is a Kubernetes resource that manages one or more Pods. Workloads automate Pod creation, scaling, updates, and recovery.

### Detailed Answer

Instead of creating Pods manually, Kubernetes uses workload controllers to maintain the desired state. Controllers continuously compare the desired state with the actual state and take corrective action when necessary.

### Production Insight

Almost all production applications are managed through workload controllers rather than standalone Pods.

### Common Mistake

❌ Thinking a Pod itself is a workload.

A Pod is managed by a workload such as a Deployment or StatefulSet.

### Follow-up Questions

* Why are Pods not enough?
* Which workload is most commonly used?

---

# 2. Why does Kubernetes use Controllers?

## Short Answer

Controllers continuously ensure that the actual cluster state matches the desired state.

### Detailed Answer

Controllers watch Kubernetes resources through the API Server. If a Pod crashes or is deleted unexpectedly, the controller creates a replacement to restore the desired state.

### Production Insight

This reconciliation loop is one of Kubernetes' core design principles and enables self-healing applications.

---

# 3. What is a Deployment?

## Short Answer

A Deployment manages stateless applications by providing scaling, self-healing, rolling updates, and rollbacks.

### Detailed Answer

A Deployment creates and manages ReplicaSets. The ReplicaSet then ensures that the correct number of Pods are running.

### Production Insight

Most web applications, REST APIs, and microservices run as Deployments.

### Common Mistake

❌ Assuming Deployments create Pods directly.

Deployments create **ReplicaSets**, and ReplicaSets create Pods.

---

# 4. What is a ReplicaSet?

## Short Answer

A ReplicaSet ensures that a specified number of identical Pods are always running.

### Detailed Answer

If a Pod fails or is deleted, the ReplicaSet automatically creates a replacement.

### Production Insight

In production, ReplicaSets are usually managed automatically by Deployments rather than created manually.

---

# 5. What is the relationship between Deployment, ReplicaSet, and Pod?

## Short Answer

Deployment → ReplicaSet → Pods

### Detailed Answer

* Deployment manages updates and rollbacks.
* ReplicaSet maintains the desired number of Pods.
* Pods run the application containers.

### Interviewer's Perspective

This is one of the most frequently asked Kubernetes interview questions.

---

# 🟡 Intermediate Level

---

# 6. What is a Rolling Update?

## Short Answer

A Rolling Update gradually replaces old Pods with new Pods while keeping the application available.

### Detailed Answer

Instead of stopping all Pods at once, Kubernetes creates new Pods and removes old ones in stages, reducing downtime.

### Production Insight

Rolling Updates are the default deployment strategy in Kubernetes.

---

# 7. What is a Rollback?

## Short Answer

A Rollback restores a previous Deployment revision if the current rollout fails.

### Commands

```bash
kubectl rollout history deployment/nginx

kubectl rollout undo deployment/nginx
```

### Production Insight

Always verify rollout health before declaring a deployment successful.

---

# 8. What is a DaemonSet?

## Short Answer

A DaemonSet ensures one Pod runs on every eligible node.

### Common Examples

* Fluent Bit
* Prometheus Node Exporter
* CNI plugins
* Security agents

### Production Insight

DaemonSets are ideal for node-level services.

---

# 9. What is a StatefulSet?

## Short Answer

A StatefulSet manages applications requiring stable identities and persistent storage.

### Characteristics

* Stable Pod names
* Stable storage
* Ordered deployment
* Ordered termination

### Common Examples

* MySQL
* PostgreSQL
* Kafka
* ZooKeeper

---

# 10. What is a Job?

## Short Answer

A Job runs Pods until a task completes successfully.

### Examples

* Database migration
* Backup
* Batch processing

---

# 11. What is a CronJob?

## Short Answer

A CronJob schedules Jobs using cron syntax.

### Examples

* Nightly backups
* Weekly reports
* Log cleanup

---

# 🔴 Senior Level

---

# 12. When would you choose a StatefulSet over a Deployment?

## Short Answer

Use a StatefulSet when Pods require stable identities, stable network names, or persistent storage.

### Production Insight

Databases and distributed systems almost always use StatefulSets.

---

# 13. Why shouldn't you manage ReplicaSets directly?

## Short Answer

Deployments provide rolling updates, rollbacks, and version management. ReplicaSets only maintain replica counts.

---

# 14. Explain the Kubernetes Reconciliation Loop.

## Short Answer

Controllers continuously compare the desired state stored in etcd with the actual cluster state and reconcile differences automatically.

### Production Insight

This reconciliation loop enables Kubernetes' self-healing behavior.

---

# 15. How does a Rolling Update work internally?

### Detailed Answer

1. Deployment creates a new ReplicaSet.
2. New Pods are created gradually.
3. Old Pods are terminated gradually.
4. Traffic shifts to healthy Pods.
5. Old ReplicaSet is retained for rollback.

---

# 🏢 Production Scenarios

---

# 16. A Deployment is stuck during rollout. What would you check?

### Investigation

```bash
kubectl rollout status deployment/<name>

kubectl describe deployment <name>

kubectl get pods

kubectl describe pod <pod>

kubectl get events
```

### Common Causes

* Readiness Probe failures
* Image pull issues
* Resource constraints
* Scheduling failures

---

# 17. Users report errors immediately after a deployment. What is your approach?

### Suggested Workflow

1. Check rollout status.
2. Review Events.
3. Verify Pod readiness.
4. Inspect application logs.
5. Roll back if necessary.
6. Perform root cause analysis before redeploying.

---

# 18. Why are readiness probes important during rolling updates?

## Short Answer

Kubernetes only sends traffic to Pods that pass the readiness probe, preventing users from reaching unready applications.

---

# 19. What workload would you choose for these scenarios?

| Scenario        | Workload    |
| --------------- | ----------- |
| Web API         | Deployment  |
| Fluent Bit      | DaemonSet   |
| PostgreSQL      | StatefulSet |
| One-time backup | Job         |
| Nightly backup  | CronJob     |

---

# 20. What are the most common mistakes with Deployments?

* Using the `latest` image tag.
* Not configuring readiness probes.
* Skipping rollout verification.
* Ignoring resource requests and limits.
* Updating production without rollback planning.

---

# Interviewer's Perspective

Senior interviewers usually move beyond definitions.

Instead of asking:

> "What is a Deployment?"

They often ask:

* Why choose a Deployment instead of a StatefulSet?
* How do rolling updates work internally?
* What happens during a failed rollout?
* How do Deployments interact with ReplicaSets?
* How would you recover from a bad deployment without downtime?

Being able to explain **why** Kubernetes behaves the way it does is what distinguishes a senior engineer from someone who has only memorized commands.
