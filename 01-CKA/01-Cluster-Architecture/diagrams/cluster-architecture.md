# Kubernetes Cluster Architecture

## High-Level Architecture
# Kubernetes Cluster Architecture

```mermaid
flowchart TD

User([User])

Kubectl[kubectl]

API[kube-apiserver]

ETCD[(etcd)]

Scheduler[kube-scheduler]

Controller[kube-controller-manager]

Node1[Worker Node 1]

Node2[Worker Node 2]

Kubelet1[kubelet]

Proxy1[kube-proxy]

Runtime1[containerd]

Pods1((Pods))

Kubelet2[kubelet]

Proxy2[kube-proxy]

Runtime2[containerd]

Pods2((Pods))

User --> Kubectl

Kubectl --> API

API --> ETCD

API --> Scheduler

API --> Controller

Scheduler --> Node1

Scheduler --> Node2

Node1 --> Kubelet1

Kubelet1 --> Runtime1

Runtime1 --> Pods1

Node1 --> Proxy1

Node2 --> Kubelet2

Kubelet2 --> Runtime2

Runtime2 --> Pods2

Node2 --> Proxy2
```

---

## Component Summary

| Component | Responsibility |
|-----------|----------------|
| kube-apiserver | Entry point to the Kubernetes API |
| etcd | Stores cluster state |
| kube-scheduler | Assigns Pods to Worker Nodes |
| kube-controller-manager | Maintains desired state |
| kubelet | Runs and monitors Pods |
| kube-proxy | Manages Service networking |
| Container Runtime | Runs containers |

---

## Production Notes

In managed Kubernetes services such as Amazon EKS, Azure AKS, and Google GKE:

- The cloud provider manages the Control Plane.
- Customers primarily manage Worker Nodes and workloads.
- etcd backups are handled by the cloud provider.
- Direct access to Control Plane nodes is generally not available.
