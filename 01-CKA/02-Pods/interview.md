# Pods - Interview Questions

> This document contains commonly asked Kubernetes Pod interview questions, ranging from beginner to senior DevOps engineer level.

---

# 1. What is a Pod?

## Short Answer

A Pod is the smallest deployable unit in Kubernetes. It encapsulates one or more containers that share networking, storage, and lifecycle.

### Detailed Answer

A Pod is an abstraction over containers. Kubernetes schedules Pods, not individual containers.

Containers inside the same Pod:

* Share the same network namespace
* Share the same IP address
* Can share storage volumes
* Start and stop together

Most Pods contain a single application container, but multi-container Pods are commonly used for sidecars such as logging or service mesh proxies.

### Production Insight

In production, long-running applications are typically managed through a Deployment rather than creating standalone Pods.

### Common Mistake

❌ "A Pod is a container."

A Pod is **not** a container. It is a wrapper around one or more containers.

### Follow-up Questions

* Can a Pod have multiple containers?
* Why do containers in the same Pod share an IP address?

---

# 2. Why are Pods considered ephemeral?

## Short Answer

Pods are designed to be temporary. They can be created, terminated, and replaced at any time.

### Detailed Answer

Pods should not be treated as permanent infrastructure.

If a Pod fails:

* Kubernetes creates a replacement Pod.
* The replacement receives a new UID.
* It may receive a different IP address.
* It may be scheduled on a different Worker Node.

Applications should therefore avoid storing important data inside the Pod's local filesystem.

### Production Insight

Persistent data should be stored using Persistent Volumes or external databases.

### Common Mistake

❌ Expecting a restarted Pod to keep the same IP address or local data.

---

# 3. What happens when you create a Pod?

## Short Answer

The API Server receives the request, stores the desired state in etcd, the Scheduler selects a node, and kubelet starts the container.

### Detailed Answer

1. User executes `kubectl apply`.
2. API Server validates the request.
3. Desired state is stored in etcd.
4. Scheduler selects the best Worker Node.
5. kubelet receives the Pod specification.
6. Container runtime pulls the image.
7. The Pod starts.
8. kubelet reports status to the API Server.

### Production Insight

This workflow is fundamental for troubleshooting scheduling and startup issues.

### Follow-up Questions

* Which component chooses the Worker Node?
* What role does kubelet play?

---

# 4. Why do containers inside the same Pod share an IP address?

## Short Answer

Because all containers in a Pod share the same network namespace.

### Detailed Answer

Containers communicate over `localhost` inside the Pod.

Benefits include:

* Simple inter-container communication
* No additional networking configuration
* Shared port space

### Production Insight

This design makes sidecar containers efficient for logging, monitoring, and service mesh implementations.

---

# 5. What is the difference between a Pod and a Deployment?

## Short Answer

A Pod runs containers. A Deployment manages Pods.

### Detailed Answer

A standalone Pod provides no self-healing or scaling.

A Deployment provides:

* Replica management
* Rolling updates
* Rollbacks
* Self-healing
* Scaling

### Production Insight

Standalone Pods are useful for testing or debugging. Production applications should generally use Deployments.

---

# 6. What is an Init Container?

## Short Answer

An Init Container runs before application containers start.

### Detailed Answer

Init Containers perform initialization tasks such as:

* Waiting for a dependency
* Downloading configuration
* Initializing storage

All Init Containers must complete successfully before application containers start.

### Common Mistake

Confusing Init Containers with Sidecar Containers.

---

# 7. What is a Sidecar Container?

## Short Answer

A Sidecar is a helper container that runs alongside the main application container.

### Examples

* Log collectors
* Monitoring agents
* Service mesh proxies
* Configuration reloaders

### Production Insight

Sidecars are widely used with service meshes such as Istio and Linkerd.

---

# 8. Explain CrashLoopBackOff.

## Short Answer

The application starts, crashes, restarts, and Kubernetes repeatedly retries with an increasing delay.

### Common Causes

* Application bug
* Incorrect command
* Missing configuration
* Database unavailable
* Port conflicts

### Troubleshooting

```bash
kubectl describe pod <pod-name>
kubectl logs <pod-name>
kubectl logs --previous <pod-name>
```

---

# 9. What is ImagePullBackOff?

## Short Answer

Kubernetes cannot download the container image.

### Common Causes

* Incorrect image name
* Wrong tag
* Private registry authentication failure
* Registry unavailable

### Troubleshooting

```bash
kubectl describe pod <pod-name>
kubectl get events
```

---

# 10. What is OOMKilled?

## Short Answer

The container exceeded its configured memory limit and the Linux kernel terminated it.

### Solution

* Increase the memory limit.
* Fix memory leaks.
* Optimize the application.

### Production Insight

Always monitor memory usage and configure appropriate requests and limits to reduce unexpected OOM kills.

---

# Interviewer's Perspective

Senior interviewers often ask follow-up questions rather than isolated facts.

For example:

**Question:** "What is a Pod?"

Typical follow-up questions:

* Why not run multiple unrelated applications in the same Pod?
* Why are Pods ephemeral?
* How do Pods communicate?
* Why should production workloads use Deployments?
* How would you troubleshoot a Pod stuck in `Pending`?
