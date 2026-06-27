# CKA - Pods

> **Goal:** Master Kubernetes Pods, their lifecycle, configuration, and troubleshooting. Pods are the smallest deployable unit in Kubernetes and form the foundation of every workload.

---

# Learning Objectives

After completing this chapter, you should be able to:

* Explain what a Pod is and why it exists.
* Create and manage Pods using `kubectl`.
* Understand Pod architecture.
* Describe the complete Pod lifecycle.
* Configure multi-container Pods.
* Use Init Containers.
* Understand restart policies.
* Configure CPU and memory requests and limits.
* Understand Kubernetes Quality of Service (QoS).
* Troubleshoot common Pod failures.
* Answer CKA and Senior DevOps interview questions related to Pods.

---

# What is a Pod?

A **Pod** is the **smallest deployable unit in Kubernetes**.

A Pod is a wrapper around one or more containers that share the same execution environment.

Containers inside the same Pod share:

* Network namespace
* IP address
* Port space
* Storage volumes
* Lifecycle

A Pod is **not** a container.

Instead, a Pod is an abstraction that manages one or more tightly coupled containers.

---

# Why Do Pods Exist?

Imagine an application with:

* Main application container
* Log collection sidecar
* Metrics exporter

These containers must:

* Start together
* Stop together
* Share networking
* Share storage

Running them inside a single Pod provides these guarantees.

---

# Pod Architecture

A Pod consists of:

* One or more containers
* Shared network namespace
* Shared storage volumes
* Pod metadata
* Pod specification

```text
Pod
│
├── Container A
├── Container B
│
├── Shared Network
├── Shared Storage
└── Shared Lifecycle
```
                 Pod
+--------------------------------------+
| IP: 10.244.1.15                      |
|                                      |
| Shared:                              |
| ✓ Network                            |
| ✓ localhost                          |
| ✓ Volumes                            |
| ✓ Pod lifecycle                      |
|                                      |
|  +-------------+  +---------------+  |
|  | Container A |  | Container B   |  |
|  |             |  |               |  |
|  | Own FS      |  | Own FS        |  |
|  | Own Env     |  | Own Env       |  |
|  | Own Limits  |  | Own Limits    |  |
|  +-------------+  +---------------+  |
+--------------------------------------+
---

# Characteristics of a Pod

Every Pod has:

* One IP address
* One hostname
* One lifecycle
* One scheduling decision

The Scheduler assigns an entire Pod to a Worker Node.

Individual containers are never scheduled independently.

---

# Pod Lifecycle

Pods move through several phases.

| Phase     | Description                                    |
| --------- | ---------------------------------------------- |
| Pending   | Accepted but not yet running                   |
| Running   | At least one container is running              |
| Succeeded | All containers exited successfully             |
| Failed    | One or more containers terminated with failure |
| Unknown   | Kubernetes cannot determine Pod state          |

> **Important:** `CrashLoopBackOff` and `ImagePullBackOff` are container states, not Pod phases.

---

# Multi-Container Pods

Although many Pods contain a single container, Kubernetes supports multiple containers in the same Pod.

Common patterns include:

* Sidecar container
* Adapter container
* Ambassador container

All containers:

* Share networking
* Share storage
* Run on the same Worker Node

---

# Init Containers

Init Containers run **before** application containers.

Typical use cases:

* Wait for a database.
* Download configuration files.
* Perform initialization tasks.
* Verify dependencies.

An Init Container must complete successfully before normal containers start.

---

# Sidecar Containers

A Sidecar Container runs alongside the main application container.

Common examples:

* Log shipping
* Service mesh proxies
* Monitoring agents
* Configuration reloaders

Unlike Init Containers, Sidecars continue running after the application starts.

---

# Restart Policy

Pods support three restart policies:

| Policy    | Description                                  |
| --------- | -------------------------------------------- |
| Always    | Restart whenever a container exits (default) |
| OnFailure | Restart only on failure                      |
| Never     | Do not restart                               |

Most long-running applications use **Always**.

---

# Resource Requests and Limits

Resources help Kubernetes schedule workloads efficiently.

### Requests

The minimum CPU and memory guaranteed to a Pod.

### Limits

The maximum CPU and memory a Pod may consume.

Example:

* CPU Request: 250m
* CPU Limit: 500m
* Memory Request: 256Mi
* Memory Limit: 512Mi

---

# Quality of Service (QoS)

Kubernetes assigns one of three QoS classes.

| QoS Class  | Description                              |
| ---------- | ---------------------------------------- |
| Guaranteed | Requests equal limits for all containers |
| Burstable  | Requests are lower than limits           |
| BestEffort | No requests or limits specified          |

Pods with higher QoS are less likely to be evicted during resource pressure.

---

# Pod Security Context

A Security Context defines security settings for a Pod or container.

Examples:

* Run as non-root
* Linux capabilities
* User ID (UID)
* Group ID (GID)
* Read-only root filesystem

Security Contexts improve workload security.

---

# Example Pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
    - name: nginx
      image: nginx:latest
      ports:
        - containerPort: 80
```

---

# Production Notes

In production environments:

* Prefer Deployments over standalone Pods.
* Configure CPU and memory requests and limits.
* Avoid the `latest` image tag.
* Configure health probes.
* Keep Pods stateless whenever possible.
* Use Secrets instead of hardcoding credentials.
* Avoid running containers as the root user.

---

# Best Practices

* One primary application per Pod.
* Keep Pods lightweight.
* Use labels consistently.
* Use resource requests and limits.
* Use Init Containers for startup tasks.
* Use Sidecars only when necessary.

---

# Summary

In this chapter you learned:

* A Pod is the smallest deployable unit in Kubernetes.
* Pods contain one or more containers.
* Containers inside a Pod share networking and storage.
* Pods progress through lifecycle phases.
* Init Containers run before application containers.
* Sidecars run alongside application containers.
* Requests and limits influence scheduling and QoS.
* Security Contexts improve workload security.
* Production workloads should usually be managed through Deployments rather than standalone Pods.

---

# References

* Kubernetes Official Documentation: https://kubernetes.io/docs/concepts/workloads/pods/
* Kubernetes Pod Lifecycle: https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/
* CKA Curriculum: https://training.linuxfoundation.org/certification/certified-kubernetes-administrator-cka/
