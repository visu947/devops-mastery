# Storage Labs

Welcome to the hands-on labs for the **CKA Storage** chapter.

These labs are designed to build your understanding from basic Kubernetes volumes to production-grade persistent storage and troubleshooting.

---

# Lab Roadmap

| Lab    | Topic                             | Difficulty |
| ------ | --------------------------------- | ---------- |
| Lab 01 | emptyDir Volume                   | ⭐          |
| Lab 02 | hostPath Volume                   | ⭐⭐         |
| Lab 03 | Shared Volumes                    | ⭐⭐         |
| Lab 04 | PersistentVolume (PV)             | ⭐⭐⭐        |
| Lab 05 | PersistentVolumeClaim (PVC)       | ⭐⭐⭐        |
| Lab 06 | StorageClass                      | ⭐⭐⭐        |
| Lab 07 | Dynamic Provisioning              | ⭐⭐⭐⭐       |
| Lab 08 | CSI (Container Storage Interface) | ⭐⭐⭐⭐       |
| Lab 09 | Stateful Applications             | ⭐⭐⭐⭐       |
| Lab 10 | Storage Troubleshooting           | ⭐⭐⭐⭐⭐      |

---

# Learning Path

```text
Temporary Storage
        │
        ▼
emptyDir
        │
        ▼
hostPath
        │
        ▼
Shared Volumes
        │
        ▼
PersistentVolume
        │
        ▼
PersistentVolumeClaim
        │
        ▼
StorageClass
        │
        ▼
Dynamic Provisioning
        │
        ▼
CSI
        │
        ▼
Stateful Applications
        │
        ▼
Production Troubleshooting
```

---

# Lab Objectives

After completing these labs, you will be able to:

* Create and mount Kubernetes Volumes.
* Understand the difference between ephemeral and persistent storage.
* Configure PersistentVolumes and PersistentVolumeClaims.
* Use StorageClasses for dynamic provisioning.
* Understand the role of CSI drivers.
* Deploy StatefulSets with persistent storage.
* Troubleshoot common storage issues in production.

---

# Recommended Order

Complete the labs in sequence:

1. Lab-01-emptyDir.md
2. Lab-02-hostPath.md
3. Lab-03-Volumes.md
4. Lab-04-PersistentVolume.md
5. Lab-05-PersistentVolumeClaim.md
6. Lab-06-StorageClass.md
7. Lab-07-Dynamic-Provisioning.md
8. Lab-08-CSI.md
9. Lab-09-Stateful-Application.md
10. Lab-10-Storage-Troubleshooting.md

Each lab builds on concepts introduced in the previous one.

---

# Skills You'll Gain

By the end of this lab series, you'll be able to:

* Explain Kubernetes storage architecture.
* Configure persistent storage for applications.
* Deploy stateful workloads using StatefulSets.
* Troubleshoot PVC, PV, StorageClass, and CSI issues.
* Apply storage best practices in production Kubernetes clusters.
* Solve common CKA storage exam scenarios.

---

# Prerequisites

Before starting these labs, you should have:

* A working Kubernetes cluster (Minikube, Kind, Docker Desktop, or a cloud cluster).
* `kubectl` configured to access the cluster.
* A basic understanding of Pods and YAML manifests.
* A default StorageClass for Labs 06–09 (recommended for dynamic provisioning).

---

# Best Practices

* Complete the labs in order.
* Read the explanation before running commands.
* Inspect resources using `kubectl describe`.
* Review events whenever something fails.
* Delete resources after completing each lab unless instructed otherwise.

---

# Expected Outcome

After completing all 10 labs, you will understand:

* Kubernetes Volumes
* emptyDir
* hostPath
* PersistentVolumes (PV)
* PersistentVolumeClaims (PVC)
* StorageClasses
* Dynamic Provisioning
* CSI Drivers
* Stateful Applications
* Production Storage Troubleshooting

These skills are essential for both the **CKA exam** and **real-world Kubernetes administration**.
