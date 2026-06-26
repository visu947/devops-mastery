# Storage - Common Mistakes

> **Learn the most common Kubernetes storage mistakes, why they happen, and how to avoid them in production.**

---

# 1. Assuming Container Filesystems Are Persistent

## Mistake

Storing application data inside the container filesystem.

## Why It Happens

Containers are ephemeral. When a Pod is recreated, its container filesystem is recreated as well.

## Example

```yaml
containers:
- name: app
  image: nginx
```

The application writes data to:

```text
/usr/share/nginx/html
```

After the Pod is deleted:

```text
Data is gone.
```

## Correct Approach

Use a Volume or PersistentVolumeClaim.

---

# 2. Using emptyDir for Persistent Data

## Mistake

Using `emptyDir` for databases.

## Why It Happens

Developers confuse **temporary storage** with **persistent storage**.

## Result

Deleting the Pod deletes all stored data.

## Correct Approach

Use:

* PersistentVolume
* PersistentVolumeClaim
* StorageClass

---

# 3. Using hostPath in Production

## Mistake

Deploying production workloads with `hostPath`.

## Why It Happens

It works well in Minikube or Docker Desktop, so teams continue using it.

## Problem

If the Pod moves to another node:

* The directory may not exist.
* Data may be unavailable.
* The application may fail.

## Correct Approach

Use network-backed persistent storage with a PVC.

---

# 4. Referencing a PersistentVolume Directly

## Mistake

Designing applications around a specific PersistentVolume.

## Why It Happens

It seems simpler in small environments.

## Problem

Applications become tightly coupled to infrastructure.

## Correct Approach

Applications should use a PersistentVolumeClaim.

Kubernetes handles the binding to the appropriate PV.

---

# 5. Ignoring StorageClass

## Mistake

Creating PVCs without understanding the available StorageClasses.

## Symptoms

```text
PVC

Pending
```

## Correct Approach

Verify:

```bash
kubectl get storageclass

kubectl get sc
```

Ensure the requested StorageClass exists.

---

# 6. Choosing the Wrong Access Mode

## Mistake

Requesting an unsupported access mode.

Example:

```yaml
accessModes:

- ReadWriteMany
```

when the storage backend only supports:

```text
ReadWriteOnce
```

## Result

PVC remains Pending or cannot be mounted.

## Correct Approach

Understand the capabilities of your storage backend before selecting an access mode.

---

# 7. Ignoring Reclaim Policies

## Mistake

Deleting a PVC without checking the PersistentVolume reclaim policy.

## Possible Outcomes

### Delete

The underlying storage is deleted.

### Retain

The storage remains and must be cleaned up manually.

## Correct Approach

Always verify:

```bash
kubectl describe pv <pv-name>
```

---

# 8. Forgetting About Volume Binding Mode

## Mistake

Not understanding how the StorageClass provisions volumes.

## Common Modes

Immediate

* Volume created immediately.

WaitForFirstConsumer

* Volume created only after a Pod is scheduled.

## Correct Approach

Choose the binding mode appropriate for your environment.

---

# 9. Not Checking Pod Events

## Mistake

Troubleshooting storage by looking only at the PVC.

## Correct Approach

Always inspect Pod events:

```bash
kubectl describe pod <pod-name>
```

Pod events often contain the exact mount error.

---

# 10. Ignoring CSI Driver Health

## Mistake

Assuming the storage backend is healthy.

## Correct Approach

Verify:

```bash
kubectl get csidriver

kubectl get csinode
```

Ensure the CSI driver is installed and functioning.

---

# 11. Using Node-Local Storage for Highly Available Workloads

## Mistake

Deploying a highly available application with node-local storage.

## Problem

If the node fails:

* The Pod moves.
* The storage does not.

## Correct Approach

Use shared or cloud-backed persistent storage.

---

# 12. Forgetting to Verify PVC Status

## Mistake

Starting application troubleshooting before confirming storage is ready.

## Correct Approach

Always begin with:

```bash
kubectl get pvc
```

Expected:

```text
STATUS

Bound
```

---

# Common Storage Mistakes

| Mistake                            | Better Practice                 |
| ---------------------------------- | ------------------------------- |
| Store data in container filesystem | Use Volumes or PVCs             |
| Use emptyDir for databases         | Use PersistentVolumes           |
| Use hostPath in production         | Use network-backed storage      |
| Reference PV directly              | Use PVC                         |
| Ignore StorageClasses              | Verify available StorageClasses |
| Wrong access mode                  | Match backend capabilities      |
| Ignore reclaim policy              | Understand Delete vs Retain     |
| Skip Pod events                    | Inspect Pod events first        |
| Ignore CSI health                  | Verify CSI components           |

---

# Production Tips

* Treat Pods as disposable.
* Store important data outside the container filesystem.
* Use dynamic provisioning whenever possible.
* Monitor PVC and PV status.
* Validate reclaim policies before deleting storage.
* Test storage failover and recovery procedures.
* Regularly back up persistent data.

---

# Interview Perspective

A common interview question is:

> **"What are the biggest mistakes teams make with Kubernetes storage?"**

A strong answer includes:

1. Assuming container filesystems are persistent.
2. Using `emptyDir` for important data.
3. Using `hostPath` in production.
4. Ignoring StorageClasses and access modes.
5. Not checking reclaim policies.
6. Troubleshooting Pods before verifying the PVC status.
7. Overlooking CSI driver health.

Avoiding these mistakes leads to more reliable, portable, and maintainable Kubernetes workloads.
