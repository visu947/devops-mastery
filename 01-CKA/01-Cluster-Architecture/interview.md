# Cluster Architecture - Interview Questions

## 1. What is Kubernetes?

**Answer:**

Kubernetes is an open-source container orchestration platform used to deploy, manage, scale, and automate containerized applications. It ensures the desired state of applications by continuously monitoring and reconciling the cluster state.

---

## 2. What are the two major components of a Kubernetes cluster?

**Answer:**

A Kubernetes cluster consists of:

* Control Plane
* Worker Nodes

The Control Plane manages the cluster, while Worker Nodes run application workloads.

---

## 3. What is the role of the kube-apiserver?

**Answer:**

The kube-apiserver is the central communication hub of Kubernetes.

Responsibilities:

* Authenticates requests
* Authorizes requests
* Validates requests
* Stores objects in etcd
* Exposes the Kubernetes REST API

Every request made using `kubectl` first reaches the API Server.

---

## 4. What is etcd?

**Answer:**

etcd is a distributed key-value database that stores the complete state of the Kubernetes cluster.

Examples:

* Pods
* Deployments
* Services
* Secrets
* ConfigMaps
* RBAC
* Nodes

If etcd data is lost and no backup exists, the cluster state cannot be recovered.

---

## 5. What does the kube-scheduler do?

**Answer:**

The Scheduler selects the most appropriate Worker Node for a Pod.

It considers:

* CPU
* Memory
* Resource requests
* Node Selector
* Node Affinity
* Pod Affinity
* Taints and Tolerations
* Volume constraints

The Scheduler assigns the Pod to a node but does not start the container.

---

## 6. What is the responsibility of the kube-controller-manager?

**Answer:**

The Controller Manager continuously compares the desired state with the current state and performs actions to reconcile any differences.

Examples include:

* Deployment Controller
* ReplicaSet Controller
* Job Controller
* Node Controller

---

## 7. What is kubelet?

**Answer:**

The kubelet is the primary agent running on every Worker Node.

Responsibilities:

* Registers the node
* Receives Pod specifications
* Starts containers
* Executes health probes
* Reports node and Pod status to the API Server

---

## 8. What is kube-proxy?

**Answer:**

kube-proxy manages Service networking.

It configures network rules so that traffic reaches the correct Pods behind a Kubernetes Service.

Depending on the cluster, it typically uses iptables or IPVS.

---

## 9. Explain the Pod creation workflow.

**Answer:**

1. User runs a `kubectl` command.
2. API Server validates the request.
3. Desired state is stored in etcd.
4. Deployment Controller creates a ReplicaSet.
5. Scheduler assigns the Pod to a Worker Node.
6. kubelet receives the assignment.
7. Container runtime pulls the image.
8. The Pod starts running.
9. kubelet reports status back to the API Server.

---

## 10. What are Static Pods?

**Answer:**

Static Pods are Pods managed directly by the kubelet instead of the API Server.

Their manifest files are typically stored in:

```text
/etc/kubernetes/manifests/
```

In kubeadm-based clusters, core Control Plane components such as the API Server, Scheduler, Controller Manager, and etcd run as Static Pods.
