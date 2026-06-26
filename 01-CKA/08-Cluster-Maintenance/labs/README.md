# Cluster Maintenance Labs

> Hands-on labs for learning Kubernetes Cluster Maintenance using a **kubeadm-based cluster**.

---

# Lab Objectives

These labs provide practical experience with maintaining Kubernetes clusters in production.

By completing these labs, you will learn how to:

* Perform safe node maintenance.
* Back up and restore etcd.
* Rotate Kubernetes certificates.
* Upgrade Kubernetes clusters using `kubeadm`.
* Understand the Version Skew Policy.
* Troubleshoot common cluster maintenance problems.

---

# Lab Progression

Complete the labs in the following order:

| Lab    | Topic                               | Difficulty |
| ------ | ----------------------------------- | ---------- |
| Lab 01 | Cordon & Uncordon Nodes             | ⭐⭐         |
| Lab 02 | Drain a Node                        | ⭐⭐⭐        |
| Lab 03 | Node Maintenance                    | ⭐⭐⭐⭐       |
| Lab 04 | etcd Backup                         | ⭐⭐⭐⭐       |
| Lab 05 | etcd Restore                        | ⭐⭐⭐⭐⭐      |
| Lab 06 | Certificate Rotation                | ⭐⭐⭐⭐       |
| Lab 07 | kubeadm Upgrade                     | ⭐⭐⭐⭐⭐      |
| Lab 08 | Version Skew                        | ⭐⭐⭐        |
| Lab 09 | Cluster Upgrade                     | ⭐⭐⭐⭐⭐      |
| Lab 10 | Cluster Maintenance Troubleshooting | ⭐⭐⭐⭐⭐      |

---

# Learning Path

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
├── Certificate Management
│
├── kubeadm Upgrade
│
├── Version Skew
│
├── Production Upgrade
│
└── Troubleshooting
```

---

# Recommended Environment

These labs are designed for a **kubeadm-based Kubernetes cluster**, such as:

* Kubernetes the Hard Way
* kubeadm on Virtual Machines
* kubeadm on cloud virtual machines
* KillerCoda Kubernetes playground
* Killercoda CKA scenarios

> **Note:** Managed Kubernetes services such as Amazon EKS, Azure AKS, and Google GKE do not provide direct access to control plane components like etcd or kubeadm. Some labs in this chapter cannot be performed on managed clusters.

---

# Prerequisites

Before starting these labs, you should understand:

* Cluster Architecture
* Pods
* Workloads
* Scheduling
* Services & Networking
* Storage
* Security

You should also have:

* A running kubeadm cluster
* `kubectl` configured
* Administrative access to the control plane node
* `sudo` privileges

---

# What You'll Learn

After completing all labs, you will be able to:

* Safely maintain Kubernetes worker nodes.
* Perform production maintenance windows.
* Back up and restore etcd.
* Rotate Kubernetes certificates.
* Upgrade Kubernetes clusters safely.
* Follow the Kubernetes Version Skew Policy.
* Troubleshoot cluster maintenance issues.
* Perform production-ready maintenance with minimal downtime.

---

# Best Practices

Throughout these labs, remember to:

* Back up etcd before major changes.
* Upgrade one node at a time.
* Upgrade the control plane before worker nodes.
* Verify cluster health after every maintenance step.
* Test restore procedures regularly.
* Follow the official Kubernetes Version Skew Policy.
* Maintain documented rollback procedures.

---

# Next Step

Begin with:

**➡️ Lab 01 – Cordon & Uncordon Nodes**

Each lab builds on the previous one, so completing them in order is strongly recommended.
