# Workloads - Common Mistakes

This document highlights common misconceptions and mistakes related to Kubernetes Workloads that frequently appear in the CKA exam, technical interviews, and production environments.

---

# Mistake 1

## Deployments create Pods directly.

❌ Incorrect

A Deployment creates and manages a ReplicaSet.

The ReplicaSet creates and manages Pods.

Correct relationship:

```text
Deployment
      │
      ▼
ReplicaSet
      │
      ▼
Pods
```

---

# Mistake 2

## ReplicaSets should be managed manually.

❌ Incorrect

ReplicaSets are usually managed automatically by Deployments.

Managing ReplicaSets directly can interfere with Deployment rollouts and rollbacks.

---

# Mistake 3

## Deployments are suitable for databases.

❌ Incorrect

Deployments are designed for stateless workloads.

Stateful applications requiring stable identities and persistent storage should use StatefulSets.

Examples:

* PostgreSQL
* MySQL
* MongoDB
* Kafka

---

# Mistake 4

## DaemonSets can be scaled using replicas.

❌ Incorrect

DaemonSets ignore replica counts.

They automatically run one Pod on every eligible node.

The number of Pods changes only when nodes are added or removed.

---

# Mistake 5

## StatefulSet Pods can be replaced in any order.

❌ Incorrect

StatefulSets use ordered deployment and ordered termination.

Pod identities are preserved.

Example:

```text
web-0
web-1
web-2
```

---

# Mistake 6

## Jobs run forever.

❌ Incorrect

Jobs run until the task completes successfully.

After completion, the Pods exit.

---

# Mistake 7

## CronJobs execute continuously.

❌ Incorrect

CronJobs create Jobs only when the schedule matches.

Example:

```cron
0 2 * * *
```

Runs once every day at 2:00 AM.

---

# Mistake 8

## Rolling Updates replace all Pods simultaneously.

❌ Incorrect

Rolling Updates gradually replace old Pods with new Pods while maintaining application availability.

This minimizes downtime.

---

# Mistake 9

## Rollbacks restore the previous Pods.

❌ Incorrect

A rollback restores the previous ReplicaSet configuration.

New Pods are created based on that ReplicaSet.

---

# Mistake 10

## Scaling only creates more Pods.

❌ Incorrect

Scaling out increases replicas.

Scaling in removes replicas.

Kubernetes attempts to maintain the desired replica count at all times.

---

# Mistake 11

## Deleting a Pod deletes the application.

❌ Incorrect

If the Pod belongs to a Deployment, ReplicaSet, or StatefulSet, the controller immediately creates a replacement Pod.

---

# Mistake 12

## DaemonSets run on every node without exception.

❌ Incorrect

DaemonSets only run on eligible nodes.

Scheduling can be influenced by:

* Node selectors
* Affinity
* Taints and tolerations

---

# Mistake 13

## Jobs and CronJobs are interchangeable.

❌ Incorrect

Job:

* Runs once.

CronJob:

* Creates Jobs according to a schedule.

---

# Mistake 14

## StatefulSets are slower than Deployments because Kubernetes is inefficient.

❌ Incorrect

StatefulSets intentionally perform ordered operations to preserve application consistency and stable identities.

---

# Mistake 15

## Using the latest image tag is a good practice.

❌ Incorrect

Using `latest` makes deployments unpredictable.

Use versioned image tags such as:

```text
myapp:v1.2.3
```

---

# Key Takeaways

* Deployments manage ReplicaSets.
* ReplicaSets manage Pods.
* Use StatefulSets for stateful workloads.
* Use DaemonSets for node-level services.
* Use Jobs for one-time tasks.
* Use CronJobs for scheduled tasks.
* Rolling Updates minimize downtime.
* Rollbacks restore previous ReplicaSets.
* Avoid using the `latest` image tag in production.
* Choose the correct workload based on the application requirements.
