# Pods - Troubleshooting Guide

> This guide provides a systematic approach to diagnosing and resolving common Pod issues in Kubernetes.

---

# Troubleshooting Workflow

Whenever a Pod is unhealthy, follow this order:

```text
kubectl get pods
        │
        ▼
kubectl describe pod
        │
        ▼
kubectl get events
        │
        ▼
kubectl logs
        │
        ▼
kubectl logs --previous
        │
        ▼
kubectl exec
        │
        ▼
kubectl top pod
```

Never skip directly to deleting the Pod. First understand **why** it failed.

---

# Scenario 1 - Pod Stuck in Pending

## Symptoms

```bash
kubectl get pods
```

Output:

```text
NAME      READY   STATUS    RESTARTS   AGE
nginx     0/1     Pending   0          2m
```

## Possible Causes

* Insufficient CPU
* Insufficient memory
* Taints
* Node selector mismatch
* PersistentVolume not available
* Unschedulable node

## Investigation

```bash
kubectl describe pod nginx
kubectl get nodes
kubectl describe node <node-name>
kubectl get events --sort-by=.lastTimestamp
```

## Resolution

* Add cluster capacity
* Fix node selectors
* Add tolerations
* Correct PVC configuration
* Free cluster resources

---

# Scenario 2 - ContainerCreating

## Symptoms

```text
STATUS: ContainerCreating
```

## Possible Causes

* Image download in progress
* Volume mounting
* Secret missing
* ConfigMap missing
* Slow storage
* Network delay

## Investigation

```bash
kubectl describe pod nginx
kubectl get events
```

---

# Scenario 3 - ImagePullBackOff

## Symptoms

```text
STATUS: ImagePullBackOff
```

## Possible Causes

* Wrong image name
* Wrong image tag
* Private registry authentication
* Registry unavailable

## Investigation

```bash
kubectl describe pod nginx
kubectl get events
```

## Resolution

* Verify image name
* Verify image tag
* Check imagePullSecrets
* Verify registry access

---

# Scenario 4 - CrashLoopBackOff

## Symptoms

```text
STATUS: CrashLoopBackOff
```

## Possible Causes

* Application crash
* Wrong startup command
* Missing environment variables
* Missing Secrets
* Database unavailable
* Configuration error

## Investigation

```bash
kubectl logs nginx
kubectl logs nginx --previous
kubectl describe pod nginx
```

## Resolution

* Fix application
* Verify configuration
* Verify dependencies
* Correct startup command

---

# Scenario 5 - OOMKilled

## Symptoms

```text
Reason: OOMKilled
```

## Cause

The container exceeded its memory limit.

## Investigation

```bash
kubectl top pod
kubectl describe pod nginx
```

## Resolution

* Increase memory limits
* Reduce memory usage
* Fix memory leaks

---

# Scenario 6 - CreateContainerConfigError

## Possible Causes

* Missing ConfigMap
* Missing Secret
* Invalid environment variable reference

## Investigation

```bash
kubectl describe pod nginx
kubectl get configmap
kubectl get secret
```

---

# Scenario 7 - CreateContainerError

## Possible Causes

* Invalid command
* Invalid entrypoint
* Invalid mount path

## Investigation

```bash
kubectl logs nginx
kubectl describe pod nginx
```

---

# Scenario 8 - Readiness Probe Failure

## Symptoms

Pod is running but receives no traffic.

## Investigation

```bash
kubectl describe pod nginx
kubectl logs nginx
```

## Resolution

* Verify endpoint
* Verify port
* Increase initialDelaySeconds if needed

---

# Scenario 9 - Liveness Probe Failure

## Symptoms

Container repeatedly restarts.

## Resolution

* Verify application health endpoint
* Tune probe configuration
* Check application startup time

---

# Scenario 10 - Startup Probe Failure

## Symptoms

Slow-starting applications restart before they become ready.

## Resolution

Configure a Startup Probe to allow additional initialization time.

---

# Production Troubleshooting Checklist

1. Check Pod status.
2. Describe the Pod.
3. Review Events.
4. Review Logs.
5. Review Previous Logs.
6. Verify Resources.
7. Verify ConfigMaps.
8. Verify Secrets.
9. Verify PVCs.
10. Verify Node health.

---

# Interviewer's Perspective

A common interview question is:

> "A Pod is stuck in Pending. What do you check first?"

A strong answer is:

1. `kubectl describe pod`
2. Review scheduling events.
3. Check node resources.
4. Verify taints and tolerations.
5. Verify PVC binding.
6. Check node readiness.

This demonstrates a structured troubleshooting approach rather than guessing.
