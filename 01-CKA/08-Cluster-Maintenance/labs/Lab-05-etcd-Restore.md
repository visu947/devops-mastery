# Lab 05 - etcd Restore

## Difficulty

⭐⭐⭐⭐⭐ Advanced

## Estimated Time

45–60 minutes

---

# CKA Objectives Covered

* Restore an etcd snapshot
* Create a new etcd data directory
* Update the etcd static Pod manifest
* Recover cluster state
* Verify successful recovery

---

# Objective

In this lab, you will:

* Restore an etcd snapshot.
* Create a new etcd data directory.
* Update the etcd static Pod configuration.
* Restart etcd.
* Verify the cluster is operational.

---

# Architecture

```mermaid
flowchart TD

Snapshot[etcd Snapshot]

Restore[etcdctl snapshot restore]

DataDir[New etcd Data Directory]

StaticPod[/etc/kubernetes/manifests/etcd.yaml]

Etcd[(etcd)]

APIServer[API Server]

Cluster[Recovered Cluster]

Snapshot --> Restore
Restore --> DataDir
DataDir --> StaticPod
StaticPod --> Etcd
Etcd --> APIServer
APIServer --> Cluster
```

---

# Prerequisites

You should have:

* A kubeadm cluster.
* A valid snapshot from Lab 04.
* Root or sudo access to the control plane node.

Example snapshot:

```text
/opt/etcd-snapshot.db
```

---

# Step 1 - Verify the Snapshot

```bash
sudo ETCDCTL_API=3 etcdctl snapshot status \
/opt/etcd-snapshot.db \
-w table
```

Expected:

```text
+----------+----------+------------+------------+
| HASH     | REVISION | TOTAL KEYS | TOTAL SIZE |
+----------+----------+------------+------------+
```

---

# Step 2 - Create a Restore Directory

```bash
sudo mkdir -p /var/lib/etcd-restore
```

Verify:

```bash
ls -ld /var/lib/etcd-restore
```

---

# Step 3 - Restore the Snapshot

```bash
sudo ETCDCTL_API=3 etcdctl snapshot restore \
/opt/etcd-snapshot.db \
--data-dir=/var/lib/etcd-restore
```

Expected:

```text
Restore completed successfully
```

The restored data is written into the new directory.

---

# Step 4 - Locate the etcd Static Pod Manifest

On kubeadm clusters:

```text
/etc/kubernetes/manifests/etcd.yaml
```

View the manifest:

```bash
sudo vi /etc/kubernetes/manifests/etcd.yaml
```

---

# Step 5 - Update the Data Directory

Locate the existing setting:

```yaml
--data-dir=/var/lib/etcd
```

Update it to:

```yaml
--data-dir=/var/lib/etcd-restore
```

---

# Step 6 - Update the Volume Mount

Locate:

```yaml
hostPath:
  path: /var/lib/etcd
```

Update it to:

```yaml
hostPath:
  path: /var/lib/etcd-restore
```

Ensure the corresponding `volumeMount` points to the same location.

---

# Step 7 - Save the Manifest

After saving the file:

* kubelet automatically detects the change.
* The etcd static Pod is recreated.
* The API server reconnects to the restored datastore.

No manual Pod restart is required.

---

# Step 8 - Verify etcd

Wait a few moments, then run:

```bash
kubectl get pods -n kube-system
```

Confirm the etcd Pod is running.

---

# Step 9 - Verify Cluster Recovery

```bash
kubectl get nodes

kubectl get pods -A
```

Verify:

* Nodes are `Ready`.
* Expected workloads exist.
* Cluster resources match the restored snapshot.

---

# Step 10 - Verify the Restored Data

Compare the cluster state with the time when the snapshot was created.

Resources created **after** the snapshot was taken will not exist after the restore.

This demonstrates that the cluster has been restored to the snapshot point in time.

---

# Verification Checklist

✅ Snapshot verified.

✅ Restore directory created.

✅ Snapshot restored.

✅ etcd manifest updated.

✅ etcd restarted.

✅ API Server healthy.

✅ Cluster state recovered.

---

# Common Errors

## Snapshot Restore Failed

Verify:

```bash
ETCDCTL_API=3 etcdctl snapshot status \
/opt/etcd-snapshot.db
```

Ensure the snapshot is valid and not corrupted.

---

## API Server Unavailable

Check:

```bash
kubectl get pods -n kube-system
```

Review:

* etcd Pod status.
* Static Pod manifest.
* Data directory paths.

---

## etcd Pod CrashLoopBackOff

Inspect:

```bash
kubectl describe pod <etcd-pod> -n kube-system
```

Review the manifest for:

* Incorrect `--data-dir`
* Incorrect volume mount
* Permission issues

---

## Nodes Remain NotReady

Verify:

```bash
kubectl get nodes

kubectl get events --sort-by=.lastTimestamp
```

Ensure the API server has successfully connected to the restored etcd instance.

---

# Production Discussion

Best practices:

* Keep multiple generations of etcd backups.
* Store backups off the control plane.
* Encrypt snapshots.
* Test restore procedures regularly.
* Document the restore process.
* Validate cluster health after every restore.

---

# Real World Notes

A restore returns the cluster to the exact state captured in the snapshot.

This means:

* Resources created after the backup are lost.
* Deleted resources may reappear.
* Application data stored outside Kubernetes (for example, in databases) is not restored by an etcd snapshot.

---

# Knowledge Check

1. Why should you restore into a new data directory instead of overwriting the existing one?
2. Which file must be updated after restoring etcd?
3. Why does kubelet automatically restart etcd?
4. What happens to resources created after the snapshot?
5. How do you verify a successful restore?

---

# Cleanup

If this was a practice restore and you want to return to the original configuration:

1. Restore the original `etcd.yaml`.
2. Point `--data-dir` back to the original directory.
3. Restart the static Pod by saving the manifest.
4. Remove the temporary restore directory if no longer needed.

---

# Challenge

1. Restore an etcd snapshot into:

```text
/var/lib/etcd-restore
```

2. Update the etcd static Pod manifest to use the restored data.

3. Verify:

```bash
kubectl get nodes

kubectl get pods -A
```

4. Explain why Kubernetes automatically recreates the etcd Pod after the manifest is modified.

5. Describe what happens to resources that were created after the snapshot was taken.
