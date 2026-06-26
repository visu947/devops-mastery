# Storage - Cheat Sheet

> **Quick revision guide for Kubernetes Storage concepts, commands, and interview tips.**

---

# Storage Hierarchy

```text
Application
     │
     ▼
Pod
     │
     ▼
Volume
     │
     ▼
PersistentVolumeClaim (PVC)
     │
     ▼
PersistentVolume (PV)
     │
     ▼
StorageClass
     │
     ▼
CSI Driver
     │
     ▼
Physical Storage
```

---

# Storage Types

| Type             | Persistent      | Scope   | Common Use               |
| ---------------- | --------------- | ------- | ------------------------ |
| emptyDir         | ❌               | Pod     | Temporary files, cache   |
| hostPath         | Depends on node | Node    | Development, testing     |
| PersistentVolume | ✅               | Cluster | Databases, stateful apps |

---

# Volume Lifecycle

```text
Pod Starts
    │
    ▼
Volume Mounted
    │
    ▼
Application Writes Data
    │
    ▼
Pod Restarts
    │
    ▼
Volume Still Available
```

> For `emptyDir`, the volume survives **container restarts** but is deleted when the **Pod** is deleted.

---

# Persistent Storage Flow

```text
User
 │
 ▼
PersistentVolumeClaim
 │
 ▼
PersistentVolume
 │
 ▼
Physical Storage
```

---

# Dynamic Provisioning

Without StorageClass:

```text
Admin Creates PV
        │
        ▼
User Creates PVC
        │
        ▼
PVC Binds to PV
```

With StorageClass:

```text
User Creates PVC
        │
        ▼
StorageClass
        │
        ▼
CSI Driver
        │
        ▼
PersistentVolume Created Automatically
```

---

# Access Modes

| Access Mode             | Description                          |
| ----------------------- | ------------------------------------ |
| ReadWriteOnce (RWO)     | Mounted read/write by one node       |
| ReadOnlyMany (ROX)      | Mounted read-only by multiple nodes  |
| ReadWriteMany (RWX)     | Mounted read/write by multiple nodes |
| ReadWriteOncePod (RWOP) | Mounted read/write by only one Pod   |

---

# Reclaim Policies

| Policy             | Behavior                                       |
| ------------------ | ---------------------------------------------- |
| Delete             | Delete the storage resource after PVC deletion |
| Retain             | Keep the storage for manual recovery           |
| Recycle *(legacy)* | Deprecated                                     |

---

# Volume Binding Modes

| Mode                 | Description                                       |
| -------------------- | ------------------------------------------------- |
| Immediate            | PV is provisioned immediately                     |
| WaitForFirstConsumer | Wait until a Pod is scheduled before provisioning |

---

# Common Commands

```bash
kubectl get pv

kubectl get pvc

kubectl get sc

kubectl describe pvc <pvc-name>

kubectl describe pv <pv-name>

kubectl describe pod <pod-name>

kubectl get csidriver

kubectl get csinode
```

---

# Storage Decision Tree

```text
Need temporary storage?
        │
        ▼
emptyDir

Need node-local storage?
        │
        ▼
hostPath

Need persistent storage?
        │
        ▼
PersistentVolumeClaim

Need automatic provisioning?
        │
        ▼
StorageClass

Need cloud storage?
        │
        ▼
CSI Driver
```

---

# Common Problems

| Problem             | Likely Cause                                 |
| ------------------- | -------------------------------------------- |
| PVC Pending         | No matching PV or StorageClass               |
| Pod Pending         | PVC not bound                                |
| Volume Mount Failed | CSI issue or incorrect configuration         |
| Data Lost           | Using emptyDir instead of persistent storage |
| Mount Read-Only     | Access mode mismatch                         |

---

# Troubleshooting Workflow

```text
Pod Pending?
     │
     ▼
PVC Bound?
     │
     ▼
PV Available?
     │
     ▼
StorageClass Exists?
     │
     ▼
CSI Driver Healthy?
     │
     ▼
Pod Events
     │
     ▼
Root Cause
```

---

# Production Best Practices

* Use PVCs instead of referencing PVs directly.
* Prefer dynamic provisioning with StorageClasses.
* Use CSI drivers supported by your storage platform.
* Select the correct access mode for your workload.
* Avoid `hostPath` in production unless required.
* Monitor storage capacity and reclaim policies.

---

# Interview Tips

### Difference between PV and PVC

* **PV** is the storage resource.
* **PVC** is the request for storage.

---

### Why use a StorageClass?

To automate PersistentVolume provisioning.

---

### What is CSI?

A standard interface that allows Kubernetes to work with different storage providers.

---

### Why does data disappear?

Because the application is writing to the container filesystem or an `emptyDir` volume instead of persistent storage.

---

# CKA Memory Map

```text
Container Filesystem
        │
        ▼
Volume
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
CSI
        │
        ▼
Stateful Application
```
