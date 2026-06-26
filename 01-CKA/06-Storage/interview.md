# Storage - Interview Questions & Answers

> **Frequently asked Kubernetes Storage interview questions for the CKA exam and Senior DevOps interviews.**

---

# Beginner Level

## 1. Why do containers lose data?

### Answer

Container filesystems are **ephemeral**.

When a Pod is deleted and recreated, the container filesystem is recreated as well, so any data stored inside the container is lost.

Persistent storage is required for applications that must retain data.

---

## 2. What is a Volume?

### Answer

A Volume is storage attached to a Pod.

Unlike the container filesystem, a Volume survives container restarts within the same Pod.

Different volume types provide different storage behavior.

---

## 3. What is emptyDir?

### Answer

An emptyDir Volume:

* Is created when a Pod starts.
* Is shared between containers in the same Pod.
* Exists until the Pod is deleted.

Typical uses:

* Temporary files
* Cache
* Scratch space

---

## 4. What is hostPath?

### Answer

hostPath mounts a directory from the Kubernetes node into a Pod.

Advantages:

* Simple
* Useful for development

Disadvantages:

* Node dependent
* Not portable
* Not recommended for most production workloads

---

## 5. What is a PersistentVolume?

### Answer

A PersistentVolume (PV) is a cluster-wide storage resource.

It exists independently of Pods and provides persistent storage.

---

## 6. What is a PersistentVolumeClaim?

### Answer

A PersistentVolumeClaim (PVC) is a request for storage.

Applications request storage using a PVC instead of directly referencing a PV.

Kubernetes binds the PVC to a suitable PersistentVolume.

---

## 7. Explain the relationship between PV and PVC.

### Answer

```text
Application
      │
      ▼
PVC
      │
      ▼
PV
      │
      ▼
Storage
```

The application uses the PVC, while Kubernetes manages the binding to the PV.

---

# Intermediate Level

## 8. What is a StorageClass?

### Answer

A StorageClass defines how PersistentVolumes are provisioned.

It specifies:

* Provisioner
* Reclaim policy
* Volume binding mode
* Storage parameters

StorageClasses enable dynamic provisioning.

---

## 9. What is dynamic provisioning?

### Answer

Without dynamic provisioning:

```text
Admin creates PV

↓

User creates PVC

↓

PVC binds to PV
```

With dynamic provisioning:

```text
User creates PVC

↓

StorageClass

↓

PV created automatically
```

Dynamic provisioning simplifies storage management.

---

## 10. What is CSI?

### Answer

CSI stands for **Container Storage Interface**.

It provides a standard way for Kubernetes to communicate with storage providers.

Examples:

* Amazon EBS CSI
* Azure Disk CSI
* Google Persistent Disk CSI
* Ceph CSI
* NFS CSI

---

## 11. What are Kubernetes access modes?

### Answer

| Access Mode             | Description              |
| ----------------------- | ------------------------ |
| ReadWriteOnce (RWO)     | Read/write by one node   |
| ReadOnlyMany (ROX)      | Read-only by many nodes  |
| ReadWriteMany (RWX)     | Read/write by many nodes |
| ReadWriteOncePod (RWOP) | Read/write by one Pod    |

---

## 12. What are reclaim policies?

### Answer

Delete

* Storage is deleted after the PVC is removed.

Retain

* Storage remains for manual recovery.

Recycle

* Deprecated.

---

# Advanced Level

## 13. Explain the storage workflow.

### Answer

```text
Application

↓

Pod

↓

Volume

↓

PVC

↓

PV

↓

StorageClass

↓

CSI

↓

Physical Storage
```

---

## 14. Why should applications use PVCs instead of PVs?

### Answer

PVCs decouple applications from the underlying storage implementation.

Benefits:

* Portability
* Flexibility
* Dynamic provisioning
* Easier maintenance

---

## 15. What happens if a PVC remains Pending?

### Answer

Possible causes:

* No matching PersistentVolume
* Missing StorageClass
* Capacity mismatch
* Access mode mismatch
* CSI driver unavailable

---

## 16. Why is hostPath discouraged in production?

### Answer

Because it ties the Pod to a specific node.

If the Pod is rescheduled to another node, the required data may not exist.

---

## 17. When would you use emptyDir?

### Answer

Use emptyDir for temporary storage such as:

* Cache
* Temporary downloads
* Intermediate processing
* Shared files between containers in the same Pod

---

## 18. Explain static vs dynamic provisioning.

### Answer

Static provisioning:

Administrator creates PersistentVolumes manually.

Dynamic provisioning:

PersistentVolumes are created automatically through a StorageClass when a PVC is created.

---

## 19. How would you troubleshoot a PVC stuck in Pending?

### Answer

Check:

```bash
kubectl get pvc

kubectl describe pvc <pvc-name>

kubectl get pv

kubectl get sc

kubectl get csidriver

kubectl get events --sort-by=.lastTimestamp
```

---

## 20. How would you troubleshoot a Pod that cannot mount storage?

### Answer

Check:

* PVC status
* PV status
* StorageClass
* CSI driver
* Pod events
* Node events

---

# Production Scenarios

## 21. Your database loses data after every restart. Why?

### Answer

The application is likely writing to:

* Container filesystem
* emptyDir

Instead of a PersistentVolumeClaim.

---

## 22. Your PVC is Pending. What is your troubleshooting process?

### Answer

1. Check PVC status.
2. Describe the PVC.
3. Verify StorageClass.
4. Check available PVs.
5. Verify access modes and requested capacity.
6. Review events.
7. Confirm the CSI driver is healthy.

---

## 23. What happens when a Pod using a PVC is rescheduled?

### Answer

The Pod is recreated.

The PVC remains.

If the storage backend supports it and the access mode permits, the volume is mounted to the new Pod, preserving the data.

---

## 24. How would you migrate storage between clusters?

### Answer

A common approach is:

* Backup the application data.
* Recreate PVs/PVCs or StorageClasses in the new cluster.
* Restore the data.
* Validate the application.

The exact process depends on the storage backend.

---

## 25. What storage type would you choose?

| Requirement                  | Recommended Choice          |
| ---------------------------- | --------------------------- |
| Temporary cache              | emptyDir                    |
| Development on a single node | hostPath                    |
| Database                     | PVC + StorageClass          |
| Cloud persistent storage     | CSI + StorageClass          |
| Shared storage across Pods   | RWX-capable storage backend |

---

# Rapid Fire

### Difference between PV and PVC?

PV is the storage resource.

PVC is the request for storage.

---

### What creates a PV automatically?

StorageClass.

---

### Does emptyDir survive a Pod deletion?

No.

---

### Does hostPath survive a Pod restart?

Yes, if the Pod is scheduled on the same node.

---

### Which storage type is recommended for databases?

PersistentVolumeClaim with an appropriate StorageClass.

---

### What does CSI stand for?

Container Storage Interface.

---

### Why use StorageClasses?

To automate storage provisioning.

---

# Interview Tips

When answering storage questions:

* Start with the concept.
* Explain the workflow.
* Mention production best practices.
* Discuss troubleshooting steps.
* Avoid diving into vendor-specific storage unless asked.

This structured approach demonstrates both conceptual understanding and operational experience.
