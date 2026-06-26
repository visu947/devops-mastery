# Cluster Architecture - Cheat Sheet

## Kubernetes Architecture

```
User
  │
kubectl
  │
API Server
  │
+-------------------------------+
| etcd | Scheduler | Controllers |
+-------------------------------+
          │
     Worker Nodes
          │
 +-----------------------------+
 | kubelet                     |
 | kube-proxy                  |
 | container runtime           |
 | Pods                        |
 +-----------------------------+
```

---

# Control Plane

| Component                | Responsibility               |
| ------------------------ | ---------------------------- |
| kube-apiserver           | Entry point for all requests |
| etcd                     | Stores cluster state         |
| kube-scheduler           | Assigns Pods to nodes        |
| kube-controller-manager  | Maintains desired state      |
| cloud-controller-manager | Cloud integration            |

---

# Worker Node

| Component          | Responsibility     |
| ------------------ | ------------------ |
| kubelet            | Runs Pods          |
| kube-proxy         | Service networking |
| containerd / CRI-O | Runs containers    |

---

# Pod Creation Flow

```
kubectl
    ↓
API Server
    ↓
etcd
    ↓
Deployment Controller
    ↓
ReplicaSet
    ↓
Scheduler
    ↓
Worker Node
    ↓
kubelet
    ↓
Container Runtime
    ↓
Pod Running
```

---

# Important Paths

```text
/etc/kubernetes/manifests/
```

Contains static Pod manifests for:

* kube-apiserver
* kube-scheduler
* kube-controller-manager
* etcd

---

# Must-Know Commands

```bash
kubectl cluster-info
kubectl get nodes
kubectl get pods -A
kubectl get pods -n kube-system
kubectl describe node <node-name>
kubectl api-resources
kubectl explain pod
```

---

# CKA Tips

* API Server is the entry point.
* etcd stores cluster state.
* Scheduler selects the node.
* kubelet creates and monitors Pods.
* kube-proxy manages Service networking.
* Controllers maintain the desired state.
* Control Plane components usually run as static Pods.
* Use `kubectl explain` whenever you're unsure of a resource field.

---

# Common Interview Questions

1. What is the role of the API Server?
2. Why is etcd important?
3. How does the Scheduler choose a node?
4. What happens after `kubectl apply`?
5. What is the difference between kubelet and kube-proxy?
