# Cluster Maintenance - Commands Cheat Sheet

> **Quick reference for Kubernetes cluster maintenance commands used in the CKA exam and production environments.**

---

# Cluster Information

## Kubernetes Version

```bash
kubectl version
```

Client version only:

```bash
kubectl version --client
```

---

## Cluster Information

```bash
kubectl cluster-info
```

---

## Node Information

List nodes:

```bash
kubectl get nodes
```

Show additional details:

```bash
kubectl get nodes -o wide
```

Describe a node:

```bash
kubectl describe node <node-name>
```

---

# Node Maintenance

## Mark Node Unschedulable

```bash
kubectl cordon <node-name>
```

Example:

```bash
kubectl cordon worker-1
```

---

## Drain a Node

Safely evict workloads:

```bash
kubectl drain <node-name> --ignore-daemonsets
```

Drain including emptyDir volumes:

```bash
kubectl drain <node-name> \
--ignore-daemonsets \
--delete-emptydir-data
```

Force drain (use with caution):

```bash
kubectl drain <node-name> \
--force \
--ignore-daemonsets
```

---

## Return Node to Service

```bash
kubectl uncordon <node-name>
```

---

# Monitor Node Status

Watch nodes:

```bash
kubectl get nodes -w
```

View node conditions:

```bash
kubectl describe node <node-name>
```

---

# View Running Pods

Pods on a specific node:

```bash
kubectl get pods -A -o wide
```

---

# etcd Backup

> Typical kubeadm clusters store certificates under `/etc/kubernetes/pki/etcd`.

Example backup:

```bash
ETCDCTL_API=3 etcdctl snapshot save snapshot.db \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key
```

---

# Verify Backup

```bash
ETCDCTL_API=3 etcdctl snapshot status snapshot.db
```

---

# Restore etcd Snapshot

```bash
ETCDCTL_API=3 etcdctl snapshot restore snapshot.db
```

> Additional restore options (such as `--data-dir`) depend on your cluster configuration.

---

# kubeadm Upgrade

Check upgrade plan:

```bash
sudo kubeadm upgrade plan
```

Upgrade control plane:

```bash
sudo kubeadm upgrade apply <version>
```

Example:

```bash
sudo kubeadm upgrade apply v1.34.0
```

Upgrade worker node configuration:

```bash
sudo kubeadm upgrade node
```

---

# kubelet Management

Check status:

```bash
systemctl status kubelet
```

Restart kubelet:

```bash
sudo systemctl restart kubelet
```

Enable kubelet:

```bash
sudo systemctl enable kubelet
```

---

# Certificate Management

Check certificate expiration:

```bash
sudo kubeadm certs check-expiration
```

Renew all certificates:

```bash
sudo kubeadm certs renew all
```

Renew a specific certificate:

```bash
sudo kubeadm certs renew apiserver
```

---

# Verify Cluster After Upgrade

Check nodes:

```bash
kubectl get nodes
```

Check system Pods:

```bash
kubectl get pods -n kube-system
```

Check all Pods:

```bash
kubectl get pods -A
```

---

# Cluster Health

API Server:

```bash
kubectl cluster-info
```

Component status (legacy clusters):

```bash
kubectl get componentstatuses
```

> Note: `componentstatuses` is deprecated in newer Kubernetes versions.

---

# Kubernetes Version

Control plane version:

```bash
kubectl version
```

Node versions:

```bash
kubectl get nodes
```

---

# Logs

kubelet logs:

```bash
journalctl -u kubelet
```

Recent logs:

```bash
journalctl -u kubelet -n 100
```

Follow logs:

```bash
journalctl -u kubelet -f
```

---

# Events

Recent events:

```bash
kubectl get events --sort-by=.lastTimestamp
```

Namespace events:

```bash
kubectl get events -n kube-system
```

---

# kube-system Components

```bash
kubectl get pods -n kube-system
```

Describe a component:

```bash
kubectl describe pod <pod-name> -n kube-system
```

---

# Maintenance Verification Checklist

Verify cluster:

```bash
kubectl cluster-info
```

Verify nodes:

```bash
kubectl get nodes
```

Verify workloads:

```bash
kubectl get pods -A
```

Verify system Pods:

```bash
kubectl get pods -n kube-system
```

Verify events:

```bash
kubectl get events --sort-by=.lastTimestamp
```

---

# Common Maintenance Workflow

```bash
# Backup etcd

# Cordon node
kubectl cordon worker-1

# Drain node
kubectl drain worker-1 --ignore-daemonsets

# Perform maintenance

# Restart kubelet (if required)
sudo systemctl restart kubelet

# Return node to service
kubectl uncordon worker-1

# Verify cluster
kubectl get nodes
kubectl get pods -A
```

---

# CKA Quick Commands

```bash
kubectl cordon <node>

kubectl drain <node> --ignore-daemonsets

kubectl uncordon <node>

kubectl get nodes

kubectl describe node <node>

kubectl cluster-info

kubectl get pods -A

kubectl get events --sort-by=.lastTimestamp

sudo kubeadm upgrade plan

sudo kubeadm certs check-expiration

systemctl status kubelet

journalctl -u kubelet
```

---

# Exam Tips

* Always **back up etcd before upgrades**.
* Upgrade the **control plane before worker nodes**.
* Drain a node before performing maintenance.
* Uncordon the node only after verifying it is healthy.
* Verify workloads and system Pods after every maintenance operation.
* Check `kube-system` if the cluster behaves unexpectedly.
