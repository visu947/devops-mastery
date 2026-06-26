# Storage - Troubleshooting Guide

> **A production-focused guide for diagnosing and resolving Kubernetes storage issues.**

---

# Storage Troubleshooting Workflow

Always troubleshoot storage problems in the following order:

```text
Pod Cannot Start
       │
       ▼
Pod Events
       │
       ▼
Volume Configuration
       │
       ▼
PVC Status
       │
       ▼
PV Status
       │
       ▼
StorageClass
       │
       ▼
CSI Driver
       │
       ▼
Node Status
       │
       ▼
Root Cause
```

Never begin by modifying YAML files. First identify where the storage workflow is failing.

---

# Scenario 1 - PVC Stuck in Pending

## Symptoms

```text
kubectl get pvc

STATUS

Pending
```

---

## Investigation

```bash
kubectl get pvc

kubectl describe pvc <pvc-name>

kubectl get pv

kubectl get sc

kubectl get events --sort-by=.lastTimestamp
```

---

## Possible Causes

* No matching PersistentVolume
* Missing StorageClass
* Requested storage too large
* Access mode mismatch
* CSI provisioner unavailable

---

## Resolution

* Verify the requested StorageClass exists.
* Ensure a matching PV is available (static provisioning).
* Check that the CSI driver is healthy (dynamic provisioning).
* Confirm capacity and access modes are compatible.

---

# Scenario 2 - Pod Cannot Mount Volume

## Symptoms

```text
Pod

ContainerCreating
```

or

```text
FailedMount
```

---

## Investigation

```bash
kubectl describe pod <pod-name>

kubectl get pvc

kubectl describe pvc <pvc-name>

kubectl get events --sort-by=.lastTimestamp
```

---

## Root Cause

The Pod cannot attach or mount the requested volume.

---

## Resolution

* Verify the PVC is **Bound**.
* Confirm the PV is available.
* Review CSI driver logs and events.
* Check node connectivity to the storage backend.

---

# Scenario 3 - PVC Bound but Pod Still Pending

## Symptoms

```text
PVC

Bound
```

Pod:

```text
Pending
```

---

## Investigation

```bash
kubectl describe pod <pod-name>

kubectl get nodes

kubectl get events
```

---

## Possible Causes

* Node scheduling constraints
* Volume binding mode
* Node affinity
* Resource shortages

---

## Resolution

Review scheduling events and confirm the selected node can access the requested storage.

---

# Scenario 4 - Data Lost After Pod Restart

## Symptoms

Application starts normally but previously written data is missing.

---

## Investigation

```bash
kubectl get pod <pod-name> -o yaml
```

Review the mounted volume type.

---

## Root Cause

The application is writing to:

* Container filesystem
* emptyDir

instead of persistent storage.

---

## Resolution

Use a PersistentVolumeClaim for data that must survive Pod recreation.

---

# Scenario 5 - Volume Mount Read-Only

## Symptoms

```text
Read-only file system
```

---

## Investigation

```bash
kubectl describe pvc <pvc-name>

kubectl describe pv <pv-name>

kubectl exec -it <pod-name> -- mount
```

---

## Possible Causes

* ReadOnly mount
* Access mode limitations
* Storage backend restrictions

---

## Resolution

Verify mount options and ensure the requested access mode is supported by the storage backend.

---

# Scenario 6 - StorageClass Not Found

## Symptoms

```text
StorageClass not found
```

---

## Investigation

```bash
kubectl get sc

kubectl describe pvc <pvc-name>
```

---

## Root Cause

The PVC references a StorageClass that does not exist.

---

## Resolution

Update the PVC to reference a valid StorageClass or create the required StorageClass.

---

# Scenario 7 - CSI Driver Issues

## Symptoms

* PVC remains Pending
* Volume mount failures
* Dynamic provisioning fails

---

## Investigation

```bash
kubectl get csidriver

kubectl get csinode

kubectl get pods -A | grep csi

kubectl get events
```

---

## Root Cause

CSI driver is unavailable or unhealthy.

---

## Resolution

* Verify the CSI controller Pods are running.
* Review CSI driver logs.
* Confirm the storage backend is reachable.

---

# Scenario 8 - PersistentVolume Released

## Symptoms

```text
STATUS

Released
```

---

## Investigation

```bash
kubectl get pv

kubectl describe pv <pv-name>
```

---

## Root Cause

The previous PVC has been deleted, but the PV has not been reclaimed.

---

## Resolution

Follow your reclaim policy:

* Delete the PV if appropriate.
* Recycle or manually recover the storage.
* Rebind using a new PV if required.

---

# Scenario 9 - Access Mode Conflict

## Symptoms

A second Pod cannot mount the volume.

---

## Investigation

```bash
kubectl describe pvc <pvc-name>

kubectl describe pv <pv-name>
```

---

## Root Cause

The requested access mode is incompatible with the workload.

Example:

```text
ReadWriteOnce
```

already mounted on another node.

---

## Resolution

Use a storage backend that supports the required access mode or redesign the workload.

---

# Scenario 10 - Dynamic Provisioning Failure

## Symptoms

PVC never binds.

No PV is created.

---

## Investigation

```bash
kubectl get pvc

kubectl describe pvc <pvc-name>

kubectl get sc

kubectl get csidriver
```

---

## Root Cause

The StorageClass cannot provision storage.

---

## Resolution

Verify:

* StorageClass configuration
* CSI provisioner
* Storage backend credentials
* Cloud provider integration

---

# Storage Troubleshooting Commands

```bash
kubectl get pv

kubectl get pvc

kubectl get sc

kubectl get csidriver

kubectl get csinode

kubectl describe pvc <pvc-name>

kubectl describe pv <pv-name>

kubectl describe pod <pod-name>

kubectl get events --sort-by=.lastTimestamp

kubectl exec -it <pod-name> -- df -h

kubectl exec -it <pod-name> -- mount
```

---

# Production Debugging Checklist

Always verify:

## Pods

```bash
kubectl get pods
```

---

## PVCs

```bash
kubectl get pvc
```

Expected:

```text
STATUS

Bound
```

---

## PersistentVolumes

```bash
kubectl get pv
```

---

## StorageClasses

```bash
kubectl get sc
```

---

## CSI Drivers

```bash
kubectl get csidriver
```

---

## Events

```bash
kubectl get events --sort-by=.lastTimestamp
```

---

## Mounted Filesystems

```bash
kubectl exec -it <pod-name> -- df -h

kubectl exec -it <pod-name> -- mount
```

---

# Common Storage Errors

| Symptom                      | Likely Cause                           |
| ---------------------------- | -------------------------------------- |
| PVC Pending                  | No PV or StorageClass mismatch         |
| Pod Pending                  | Volume not mounted                     |
| FailedMount                  | CSI or node issue                      |
| Data lost                    | Using container filesystem or emptyDir |
| Read-only filesystem         | Access mode or mount configuration     |
| Released PV                  | PVC deleted, reclaim policy applied    |
| Dynamic provisioning failure | StorageClass or CSI issue              |

---

# Production Tips

* Verify the PVC before troubleshooting the Pod.
* Inspect Pod events for mount failures.
* Use `kubectl describe` to identify binding issues.
* Understand your StorageClass reclaim policy.
* Monitor CSI driver health.
* Test backup and recovery procedures regularly.

---

# Interviewer's Perspective

A common senior interview question is:

> **"A Pod is stuck in `ContainerCreating` because of a storage issue. How would you troubleshoot it?"**

A strong answer includes:

1. Check Pod events.
2. Verify the PVC status.
3. Confirm the PV is available.
4. Inspect the StorageClass.
5. Verify the CSI driver.
6. Review cluster events.
7. Confirm node access to the storage backend.
8. Apply the smallest safe fix and verify the volume mounts successfully.

This systematic approach demonstrates production-ready troubleshooting skills rather than trial-and-error.
