
---

## 3️⃣ worker-node.md

# Worker Node

## Components

```text
Worker Node

│

├── kubelet

├── kube-proxy

├── container runtime

└── Pods


kubelet
Registers node
Runs Pods
Executes probes
Reports status
kube-proxy
Configures Service networking
Maintains iptables/IPVS rules
Container Runtime

Common runtimes:

containerd
CRI-O
Summary

The Worker Node is responsible for running application workloads assigned by the Scheduler.
