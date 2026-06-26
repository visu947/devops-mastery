# CKA - Cluster Maintenance

> **Goal:** Learn how to safely maintain, upgrade, back up, and recover Kubernetes clusters using production best practices.

---

# 📚 Chapter Contents

* Learning Objectives
* Why Cluster Maintenance Matters
* Cluster Maintenance Overview
* Node Maintenance
* etcd Backup & Restore
* Certificate Management
* Kubernetes Upgrades
* Version Skew Policy
* Production Best Practices
* Summary
* References

---

# Learning Objectives

After completing this chapter, you will be able to:

* Perform safe node maintenance using `cordon`, `drain`, and `uncordon`.
* Understand the purpose of etcd and perform backups and restores.
* Manage Kubernetes certificates.
* Upgrade Kubernetes clusters using `kubeadm`.
* Understand the Kubernetes Version Skew Policy.
* Perform cluster maintenance with minimal downtime.
* Troubleshoot common maintenance issues.
* Answer CKA and Senior DevOps interview questions confidently.

---

# Why Cluster Maintenance Matters

Deploying applications is only part of running Kubernetes.

A production Kubernetes cluster requires ongoing maintenance to ensure:

* High availability
* Security
* Performance
* Disaster recovery
* Version compatibility

Common maintenance tasks include:

* Draining worker nodes
* Upgrading Kubernetes
* Backing up etcd
* Rotating certificates
* Replacing failed nodes
* Applying security patches

---

# Cluster Maintenance Overview

```text
Administrator
      │
      ▼
Node Maintenance
      │
      ▼
etcd Backup
      │
      ▼
Certificate Management
      │
      ▼
Cluster Upgrade
      │
      ▼
Health Verification
```

Cluster maintenance is a continuous operational process throughout the lifecycle of a Kubernetes cluster.

---

# Node Maintenance

Before performing maintenance on a worker node:

1. Mark the node unschedulable using `cordon`.
2. Safely evict workloads using `drain`.
3. Perform maintenance or upgrades.
4. Return the node to service using `uncordon`.

This minimizes application downtime while ensuring safe maintenance.

---

# etcd Backup & Restore

etcd is the primary datastore for Kubernetes.

It stores the complete cluster state, including:

* Pods
* Deployments
* Services
* ConfigMaps
* Secrets
* RBAC objects
* PersistentVolumeClaims
* Namespaces

Regular backups are essential for disaster recovery.

---

# Certificate Management

Kubernetes secures communication using TLS certificates.

Certificates are used between:

* kubectl ↔ API Server
* API Server ↔ kubelet
* API Server ↔ etcd
* Scheduler ↔ API Server
* Controller Manager ↔ API Server

Certificates should be monitored and rotated before expiration.

---

# Kubernetes Upgrades

A typical upgrade sequence is:

```text
Backup etcd

↓

Upgrade Control Plane

↓

Verify Control Plane

↓

Drain Worker Node

↓

Upgrade Worker Node

↓

Uncordon Worker Node

↓

Repeat for Remaining Workers
```

Always upgrade the control plane before upgrading worker nodes.

---

# Version Skew Policy

Kubernetes components support only limited version differences.

Examples:

* `kubectl` should be within one minor version of the API server.
* kubelets should remain within the supported version skew of the control plane.
* Control plane components should run compatible versions.

Following the Version Skew Policy ensures cluster stability during upgrades.

---

# Production Maintenance Workflow

```text
Plan Maintenance

↓

Backup etcd

↓

Cordon Node

↓

Drain Node

↓

Upgrade or Repair

↓

Restart Required Services

↓

Uncordon Node

↓

Verify Cluster Health
```

Never skip health verification after maintenance.

---

# Production Best Practices

* Always back up etcd before upgrades.
* Upgrade one node at a time.
* Verify workloads after each maintenance step.
* Rotate certificates before expiration.
* Follow the Version Skew Policy.
* Test upgrades in a non-production environment first.
* Maintain rollback procedures.
* Monitor cluster health throughout the maintenance window.

---

# Summary

In this chapter you will learn:

* Node maintenance
* etcd backup and restore
* Certificate management
* Kubernetes upgrades
* Version compatibility
* Cluster maintenance workflows
* Production troubleshooting

These skills are essential for both the **CKA exam** and real-world Kubernetes administration.

---

# Chapter Roadmap

```text
Cluster Maintenance

│

├── Node Maintenance
│      ├── Cordon
│      ├── Drain
│      └── Uncordon
│
├── etcd
│      ├── Backup
│      └── Restore
│
├── Certificates
│
├── kubeadm Upgrade
│
├── Version Skew
│
├── Production Maintenance
│
└── Troubleshooting
```

---

# References

* Kubernetes Documentation
* kubeadm Documentation
* etcd Documentation
* Kubernetes Certificate Management
* Kubernetes Version Skew Policy
* Certified Kubernetes Administrator (CKA) Curriculum
