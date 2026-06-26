# Worker Node Components

## Worker Node Architecture

```text
+--------------------------------------+
|            Worker Node               |
|--------------------------------------|
| kubelet                              |
| kube-proxy                           |
| Container Runtime (containerd/CRI-O) |
|--------------------------------------|
| Pods                                 |
+--------------------------------------+
```

---

## kubelet

Responsibilities:

- Registers the node with the cluster.
- Watches for assigned Pods.
- Starts containers.
- Executes health probes.
- Reports node and Pod status.

---

## kube-proxy

Responsibilities:

- Configures Service networking.
- Maintains iptables or IPVS rules.
- Enables communication between Services and Pods.

---

## Container Runtime

Common runtimes:

- containerd
- CRI-O

The container runtime is responsible for pulling images and running containers.

---

## Summary

A Worker Node executes workloads assigned by the Scheduler while kubelet, kube-proxy, and the container runtime work together to keep applications running.
