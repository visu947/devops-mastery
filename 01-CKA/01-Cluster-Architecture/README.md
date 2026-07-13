# CKA - Cluster Architecture

> **Goal:** Understand how a Kubernetes cluster is built, how each component works, and how they communicate with one another.

---

# Learning Objectives

After completing this chapter, you will be able to:

* Explain the Kubernetes architecture.
* Identify all Control Plane and Worker Node components.
* Understand the responsibility of each component.
* Describe the complete Pod creation workflow.
* Troubleshoot common cluster architecture issues.
* Answer CKA and Senior DevOps interview questions related to cluster architecture.

---

# What is Kubernetes?

Kubernetes (K8s) is an open-source container orchestration platform that automates the deployment, scaling, networking, and lifecycle management of containerized applications.

Instead of managing individual containers manually, Kubernetes manages an entire cluster of machines and ensures applications are always running in their desired state.

### Core Features

* Container orchestration
* Automatic scheduling
* Self-healing
* Service discovery
* Load balancing
* Rolling updates
* Automatic scaling
* Secret and configuration management

---

# Why Kubernetes?

Before Kubernetes, applications were often deployed manually or with simple scripts. This approach introduced several challenges:

* Difficult to scale applications
* No automatic recovery after failures
* Manual deployments
* Complex networking
* Poor resource utilization

Kubernetes solves these problems by continuously monitoring the cluster and reconciling the actual state with the desired state.

---

# Kubernetes Architecture

A Kubernetes cluster consists of two major parts:

1. **Control Plane**
2. **Worker Nodes**

```
                        User
                         |
                     kubectl
                         |
                         v
                +-------------------+
                | kube-apiserver    |
                +-------------------+
                  |     |      |
                  |     |      |
               etcd  Scheduler  Controller Manager
                  |
        -------------------------
        |                       |
+------------------+   +------------------+
|   Worker Node 1  |   |   Worker Node 2  |
|------------------|   |------------------|
| kubelet          |   | kubelet          |
| kube-proxy       |   | kube-proxy       |
| containerd       |   | containerd       |
| Pods             |   | Pods             |
+------------------+   +------------------+
```

---

# Control Plane

The Control Plane is the brain of Kubernetes.

It receives requests, stores the cluster state, schedules workloads, and continuously ensures the cluster matches the desired configuration.

The main Control Plane components are:

* kube-apiserver
* etcd
* kube-scheduler
* kube-controller-manager
* cloud-controller-manager

---

# kube-apiserver

## Purpose

The API Server is the entry point to the Kubernetes cluster.

Every operation—whether performed by a user, automation tool, or internal Kubernetes component—passes through the API Server.

Examples:

* `kubectl`
* GitOps tools
* CI/CD pipelines
* Controllers
* Scheduler
* Dashboard

Example:

```bash
kubectl get pods
```

The request flow is:

```
kubectl
   ↓
API Server
   ↓
etcd
```

### Responsibilities

* Authenticates requests
* Authorizes requests
* Validates requests
* Stores cluster state in etcd
* Exposes the Kubernetes REST API

---

# etcd

## Purpose

etcd is the distributed key-value database used by Kubernetes.

It stores the entire cluster state.

Examples of stored objects include:

* Nodes
* Pods
* Deployments
* Services
* ConfigMaps
* Secrets
* RBAC configuration
* Namespaces

### Why it matters

If etcd becomes unavailable, the cluster cannot accept or process new changes.

If etcd data is lost and no backup exists, the cluster state is lost.

### Common CKA Command

```bash
ETCDCTL_API=3 etcdctl snapshot save backup.db

ETCDCTL_API=3 etcdctl --endpoints=https://127.0.0.1:2379 \
 --cacert="/etc/kubernetes/pki/etcd/ca.crt" \
 --cert="/etc/kubernetes/pki/etcd/server.crt" \
 --key="/etc/kubernetes/pki/etcd/server.key" \
snapshot save /opt/cluster_backup.db 2>&1 | tee backup.txt

etcdutl snapshot restore /opt/cluster_backup.db \
  --data-dir=/root/default.etcd > restore.txt 2>&1

```


# Kubernetes Control Plane Upgrade (kubeadm)

## 1. Check available versions

```bash
apt update
apt list --upgradable
apt-cache madison kubeadm
```

## 2. Check current Kubernetes versions

```bash
kubectl get nodes
kubeadm version
kubelet --version
```

## 3. Drain the control plane

```bash
kubectl drain controlplane --ignore-daemonsets
```

If required:

```bash
kubectl drain controlplane --ignore-daemonsets --delete-emptydir-data
```

---

## 4. Upgrade kubeadm

```bash
apt-mark unhold kubeadm
apt update
apt-get install -y kubeadm=<TARGET_VERSION>
apt-mark hold kubeadm
```

Example:

```bash
apt-get install -y kubeadm=1.35.2-1.1
```

Verify:

```bash
kubeadm version
```

---

## 5. Upgrade the Control Plane

```bash
kubeadm upgrade plan
kubeadm upgrade apply v<TARGET_VERSION>
```

Example:

```bash
kubeadm upgrade apply v1.35.2
```

---

## 6. Upgrade kubelet and kubectl

```bash
apt-mark unhold kubelet kubectl

apt-get install -y kubelet=<TARGET_VERSION>
apt-get install -y kubectl=<TARGET_VERSION>

apt-mark hold kubelet kubectl
```

Example:

```bash
apt-get install -y kubelet=1.35.2-1.1
apt-get install -y kubectl=1.35.2-1.1
```

---

## 7. Restart kubelet

```bash
systemctl daemon-reload
systemctl restart kubelet
systemctl status kubelet
```

---

## 8. Uncordon the node

```bash
kubectl uncordon controlplane
```

---

## 9. Verify

```bash
kubectl get nodes
kubeadm version
kubelet --version
kubectl version --client
```

---

# Memory Flow

```
Check Versions
      ↓
Drain
      ↓
Upgrade kubeadm
      ↓
Plan
      ↓
Apply
      ↓
Upgrade kubelet & kubectl
      ↓
Restart kubelet
      ↓
Uncordon
      ↓
Verify
```

---

# Worker Node Upgrade

```
Drain
      ↓
SSH to Worker
      ↓
Upgrade kubeadm
      ↓
kubeadm upgrade node
      ↓
Upgrade kubelet & kubectl
      ↓
Restart kubelet
      ↓
Uncordon
      ↓
Verify
```

### Difference to Remember

Control Plane:

```bash
kubeadm upgrade apply v<TARGET_VERSION>
```

Worker Node:

```bash
kubeadm upgrade node
```



---

# kube-scheduler

## Purpose

The Scheduler decides **where** a Pod should run.

It evaluates all available worker nodes and selects the most suitable one.

### Scheduling Factors

* Available CPU
* Available Memory
* Node Selector
* Node Affinity
* Pod Affinity
* Pod Anti-Affinity
* Taints and Tolerations
* Resource Requests
* Persistent Volume requirements

> **Important:** The Scheduler only selects the node. It does **not** start the container.

---

# Kubernetes Controllers

The **kube-controller-manager** runs multiple controllers. Each controller continuously watches the Kubernetes API Server, compares the **desired state** with the **current state**, and takes corrective action whenever differences are detected.

Controllers do **not** communicate directly with each other. They interact indirectly through the **API Server** using the reconciliation loop.

## Common Controllers

### Deployment Controller

* Monitors Deployment resources.
* Creates or updates ReplicaSets.
* Manages rolling updates and rollbacks.

### ReplicaSet Controller

* Ensures the desired number of Pod replicas are running.
* Creates new Pods when replicas are missing.
* Removes excess Pods when replicas are reduced.

### Node Controller

* Monitors the health of worker nodes.
* Marks nodes as `NotReady` when they become unreachable.
* Evicts Pods from unhealthy nodes after configured timeouts.

### Job Controller

* Monitors Job resources.
* Creates Pods to complete one-time tasks.
* Tracks successful and failed Pod executions.
* Marks Jobs as completed when the required number of successful Pods finish.

### EndpointSlice Controller

* Monitors Services and Pods.
* Creates and updates EndpointSlice resources.
* Ensures Services always have an up-to-date list of healthy backend Pods.

## Controller Responsibilities

Controllers answer the question:

> **"What should exist?"**

For example, if a Deployment is scaled from **1** to **5** replicas:

1. The **Deployment Controller** updates the ReplicaSet.
2. The **ReplicaSet Controller** creates four new Pod objects.
3. The **Scheduler** assigns those Pods to worker nodes.
4. The **kubelet** on each assigned node starts the containers.

## Key Takeaway

Controllers **do not run containers** and **do not choose nodes**.

Their responsibility is to continuously reconcile the cluster until the **actual state matches the desired state** stored in Kubernetes.


# cloud-controller-manager

This component integrates Kubernetes with cloud providers.

Examples:

* Creating cloud load balancers
* Managing cloud storage
* Managing cloud routes
* Monitoring cloud instances

In managed Kubernetes services such as Amazon EKS, Azure AKS, or Google GKE, many cloud-specific operations are handled through this component.

---

# Worker Nodes

Worker Nodes are the machines where application Pods actually run.

Each Worker Node contains:

* kubelet
* kube-proxy
* Container Runtime

---

# kubelet

The kubelet is the primary node agent.

Responsibilities:

* Registers the node with the cluster
* Watches for Pod assignments
* Starts containers
* Reports node health
* Reports Pod status
* Executes health probes

Without kubelet, workloads cannot run on the node.

---

# kube-proxy

kube-proxy manages Service networking.

Responsibilities:

* Configures network rules
* Enables communication between Services and Pods
* Supports ClusterIP, NodePort, and LoadBalancer Services

Depending on the cluster configuration, kube-proxy commonly uses:

* iptables
* IPVS

---

# Container Runtime

The container runtime is responsible for creating and running containers.

Common runtimes include:

* containerd
* CRI-O

Modern Kubernetes clusters no longer use Docker as the runtime.

---

# Pod Creation Workflow

When you execute:

```bash
kubectl create deployment nginx --image=nginx
```

The following sequence occurs:

1. `kubectl` sends the request to the API Server.
2. The API Server authenticates, authorizes, and validates the request.
3. The desired state is stored in etcd.
4. The Deployment Controller creates a ReplicaSet.
5. The Scheduler selects the most suitable Worker Node.
6. The kubelet on that node receives the Pod assignment.
7. The container runtime pulls the image.
8. The container is started.
9. kubelet reports the Pod status back to the API Server.

---

# Summary

In this chapter you learned:

* Kubernetes consists of a Control Plane and Worker Nodes.
* The API Server is the central communication point.
* etcd stores the cluster's desired state.
* The Scheduler selects nodes for new Pods.
* Controllers maintain the desired state.
* kubelet manages workloads on each Worker Node.
* kube-proxy provides Service networking.
* The container runtime runs containers.
* Understanding the Pod creation workflow is fundamental for both the CKA exam and real-world troubleshooting.

---

# References

* Kubernetes Official Documentation: https://kubernetes.io/docs/
* CKA Curriculum: https://training.linuxfoundation.org/certification/certified-kubernetes-administrator-cka/
