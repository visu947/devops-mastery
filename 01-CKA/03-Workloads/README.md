# CKA - Workloads

> **Goal:** Learn how Kubernetes manages applications using controllers such as Deployments, ReplicaSets, DaemonSets, StatefulSets, Jobs, and CronJobs.

---

# 📚 Chapter Contents

- Learning Objectives
- What is a Workload?
- Why Kubernetes Uses Controllers
- Deployment
- ReplicaSet
- DaemonSet
- StatefulSet
- Job
- CronJob
- Rolling Updates
- Rollbacks
- Scaling
- Deployment Strategies
- Production Notes
- Best Practices
- Summary
- References

---

# Learning Objectives

After completing this chapter, you should be able to:

- Explain why Kubernetes uses controllers.
- Create and manage Deployments.
- Understand ReplicaSets.
- Perform rolling updates.
- Roll back failed deployments.
- Scale applications manually.
- Explain DaemonSets and their use cases.
- Explain StatefulSets and stable identities.
- Create Jobs and CronJobs.
- Choose the correct workload for different production scenarios.
- Troubleshoot common workload issues.
- Answer CKA and Senior DevOps interview questions.

---

# What is a Workload?

A Kubernetes workload is an API resource that manages Pods.

Rather than creating Pods directly, production applications are typically managed through workload controllers.

A workload ensures that the desired number of Pods are running and automatically replaces failed Pods.

---

# Why Kubernetes Uses Controllers

Pods are ephemeral.

If a Pod fails, Kubernetes needs a mechanism to create a replacement automatically.

Controllers continuously compare:

- Desired state
- Actual state

If they differ, Kubernetes takes corrective action.

This is known as the **reconciliation loop**.

---

# Types of Workloads

| Workload | Primary Use Case |
|----------|------------------|
| Deployment | Stateless applications |
| ReplicaSet | Maintains a fixed number of Pods |
| DaemonSet | One Pod per node |
| StatefulSet | Stateful applications |
| Job | Run a task once |
| CronJob | Run tasks on a schedule |

---

# Deployment

A Deployment is the most commonly used workload in Kubernetes.

It provides:

- Self-healing
- Scaling
- Rolling updates
- Rollbacks
- Replica management

Production web applications typically run as Deployments.

---

# ReplicaSet

A ReplicaSet ensures that the desired number of identical Pods are running.

Deployments automatically create and manage ReplicaSets.

In production, you generally create Deployments rather than ReplicaSets directly.

---

# DaemonSet

A DaemonSet ensures that one Pod runs on every eligible node.

Common examples:

- Fluent Bit
- Prometheus Node Exporter
- CNI plugins
- Security agents

---

# StatefulSet

A StatefulSet manages applications that require stable identities.

Features include:

- Stable Pod names
- Stable storage
- Ordered deployment
- Ordered termination

Common examples:

- MySQL
- PostgreSQL
- MongoDB
- Kafka
- ZooKeeper

---

# Job

A Job runs one or more Pods until a task completes successfully.

Common use cases:

- Database migration
- Backup
- Data import
- Batch processing

---

# CronJob

A CronJob creates Jobs on a schedule.

Examples:

- Nightly backups
- Report generation
- Log cleanup
- Certificate renewal

---

# Rolling Updates

Deployments support zero-downtime updates by gradually replacing old Pods with new Pods.

Benefits:

- Reduced downtime
- Controlled rollout
- Easy monitoring
- Safer deployments

---

# Rollbacks

If a deployment introduces problems, Kubernetes can roll back to a previous ReplicaSet.

This enables quick recovery from failed releases.

---

# Scaling

Applications can be scaled by changing the number of replicas.

Scaling can be:

- Manual
- Automatic (Horizontal Pod Autoscaler)

---

# Deployment Strategies

Common deployment strategies include:

- Rolling Update
- Recreate
- Blue/Green
- Canary

Rolling Update is the default strategy in Kubernetes.

---

# Production Notes

- Prefer Deployments for stateless applications.
- Use StatefulSets only when stable identities are required.
- Deploy DaemonSets for node-level agents.
- Use Jobs for one-time tasks.
- Use CronJobs for scheduled tasks.
- Configure readiness probes before rolling updates.
- Use labels consistently for workload management.

---

# Best Practices

- Never manage production applications with standalone Pods.
- Use Deployments for web applications.
- Configure resource requests and limits.
- Use rolling updates instead of deleting Pods manually.
- Verify deployments after every rollout.
- Use namespaces to organize workloads.

---

# Summary

In this chapter you learned:

- Why Kubernetes uses controllers.
- The purpose of each workload type.
- How Deployments manage applications.
- The relationship between Deployments and ReplicaSets.
- When to use DaemonSets and StatefulSets.
- How Jobs and CronJobs execute tasks.
- How rolling updates, rollbacks, and scaling work.

---

# References

- Kubernetes Workloads Documentation
- Kubernetes Deployments Documentation
- Kubernetes StatefulSets Documentation
- Kubernetes Jobs Documentation
- Kubernetes CronJobs Documentation
- CKA Curriculum
