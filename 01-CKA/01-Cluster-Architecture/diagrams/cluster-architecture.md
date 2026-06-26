# Kubernetes Cluster Architecture

## High-Level Architecture

```text
                    User
                     │
                 kubectl
                     │
                     ▼
            +------------------+
            | kube-apiserver   |
            +------------------+
               │      │      │
               │      │      │
             etcd  Scheduler Controllers
               │
        -------------------------
        │                       │
+------------------+   +------------------+
| Worker Node 1    |   | Worker Node 2    |
|------------------|   |------------------|
| kubelet          |   | kubelet          |
| kube-proxy       |   | kube-proxy       |
| containerd       |   | containerd       |
| Pods             |   | Pods             |
+------------------+   +------------------+

```
## Component Summary
----------------------------------------------
| Component         | Responsibility         |
| ----------------- | ---------------------- |
| API Server        | Entry point            |
| etcd              | Cluster database       |
| Scheduler         | Selects node           |
| Controllers       | Maintain desired state |
| kubelet           | Runs Pods              |
| kube-proxy        | Service networking     |
| container runtime | Runs containers        |
