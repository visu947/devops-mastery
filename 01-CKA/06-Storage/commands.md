# Storage - Commands

> **Quick reference for Kubernetes Storage commands used in the CKA exam, production environments, and technical interviews.**

---

# Table of Contents

1. Volumes
2. PersistentVolumes
3. PersistentVolumeClaims
4. StorageClasses
5. CSI
6. Pods Using Storage
7. Troubleshooting
8. Useful Output Formats
9. Explain Resources
10. Production Tips

---

# 1. Volumes

## View Pod Volumes

```bash
kubectl describe pod <pod-name>
```

---

## View Volume Configuration

```bash
kubectl get pod <pod-name> -o yaml
```

---

## View Mounted Volumes

```bash
kubectl describe pod <pod-name> | grep -A20 Volumes
```

---

## View Volume Mounts

```bash
kubectl describe pod <pod-name> | grep -A20 Mounts
```

---

# 2. PersistentVolumes (PV)

## List PersistentVolumes

```bash
kubectl get pv
```

---

## Describe PersistentVolume

```bash
kubectl describe pv <pv-name>
```

---

## View PersistentVolume YAML

```bash
kubectl get pv <pv-name> -o yaml
```

---

## Delete PersistentVolume

```bash
kubectl delete pv <pv-name>
```

---

# 3. PersistentVolumeClaims (PVC)

## List PersistentVolumeClaims

```bash
kubectl get pvc
```

---

## Describe PersistentVolumeClaim

```bash
kubectl describe pvc <pvc-name>
```

---

## View PVC YAML

```bash
kubectl get pvc <pvc-name> -o yaml
```

---

## Delete PVC

```bash
kubectl delete pvc <pvc-name>
```

---

# 4. StorageClasses

## List StorageClasses

```bash
kubectl get storageclass
```

Short form:

```bash
kubectl get sc
```

---

## Describe StorageClass

```bash
kubectl describe storageclass <storageclass-name>
```

---

## View StorageClass YAML

```bash
kubectl get sc <storageclass-name> -o yaml
```

---

# 5. CSI

## View CSI Drivers

```bash
kubectl get csidriver
```

---

## Describe CSI Driver

```bash
kubectl describe csidriver <driver-name>
```

---

## View CSI Nodes

```bash
kubectl get csinode
```

---

## Describe CSI Node

```bash
kubectl describe csinode <node-name>
```

---

# 6. Pods Using Storage

## View Pod Details

```bash
kubectl describe pod <pod-name>
```

---

## View Mounted Filesystems

```bash
kubectl exec -it <pod-name> -- df -h
```

---

## View Mounted Volumes

```bash
kubectl exec -it <pod-name> -- mount
```

---

## Verify Files

```bash
kubectl exec -it <pod-name> -- ls -l /mount/path
```

---

## Create Test File

```bash
kubectl exec -it <pod-name> -- sh
```

Inside the container:

```sh
echo "Hello Kubernetes" > /mount/path/test.txt

cat /mount/path/test.txt
```

---

# 7. Troubleshooting

## List PVs

```bash
kubectl get pv
```

---

## List PVCs

```bash
kubectl get pvc
```

---

## Describe PVC

```bash
kubectl describe pvc <pvc-name>
```

---

## Verify Pod Events

```bash
kubectl describe pod <pod-name>
```

---

## View Cluster Events

```bash
kubectl get events --sort-by=.lastTimestamp
```

---

## Check StorageClass

```bash
kubectl get sc
```

---

## Verify CSI Drivers

```bash
kubectl get csidriver
```

---

## View Node Storage Information

```bash
kubectl get csinode
```

---

# 8. Useful Output Formats

## PV YAML

```bash
kubectl get pv <pv-name> -o yaml
```

---

## PVC YAML

```bash
kubectl get pvc <pvc-name> -o yaml
```

---

## StorageClass YAML

```bash
kubectl get sc <storageclass-name> -o yaml
```

---

## Pod YAML

```bash
kubectl get pod <pod-name> -o yaml
```

---

# 9. Explain Resources

## PersistentVolume

```bash
kubectl explain pv
```

---

## PersistentVolume Spec

```bash
kubectl explain pv.spec
```

---

## PersistentVolumeClaim

```bash
kubectl explain pvc
```

---

## PVC Spec

```bash
kubectl explain pvc.spec
```

---

## StorageClass

```bash
kubectl explain storageclass
```

---

## Pod Volume

```bash
kubectl explain pod.spec.volumes
```

---

# 10. Most Common Storage Troubleshooting Commands

```bash
kubectl get pv

kubectl get pvc

kubectl get sc

kubectl get csidriver

kubectl describe pvc <pvc-name>

kubectl describe pv <pv-name>

kubectl describe pod <pod-name>

kubectl get events --sort-by=.lastTimestamp

kubectl exec -it <pod-name> -- df -h

kubectl exec -it <pod-name> -- mount
```

---

# Production Tips

✅ Use PVCs instead of directly referencing PersistentVolumes.

✅ Prefer StorageClasses with dynamic provisioning.

✅ Verify PVC status before troubleshooting Pods.

✅ Check StorageClass if a PVC remains in the **Pending** state.

✅ Verify CSI drivers are installed and healthy.

✅ Use `kubectl describe` to inspect mount and provisioning events.

---

# CKA Exam Tips

✔ PVC stuck in **Pending**?

Check:

* StorageClass
* Available PVs
* Access modes
* Requested capacity

✔ Pod cannot mount storage?

Check:

* PVC status
* Pod events
* CSI driver
* Node accessibility

✔ Data disappears after Pod recreation?

Verify whether the application is using:

* `emptyDir` (ephemeral)
* `hostPath`
* PersistentVolumeClaim

---

# References

* Kubernetes Volumes Documentation
* Persistent Volumes Documentation
* StorageClasses Documentation
* CSI Documentation
