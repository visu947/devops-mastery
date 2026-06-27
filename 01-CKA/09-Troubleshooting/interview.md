# Troubleshooting - Interview Questions

> **Production-focused Kubernetes troubleshooting interview questions with detailed answers for CKA preparation and Senior DevOps interviews.**

---

# Interview Strategy

During troubleshooting interviews, employers evaluate:

* Your troubleshooting methodology
* Your ability to identify the root cause
* Your knowledge of Kubernetes internals
* Your debugging process
* Your production experience

A structured troubleshooting process is often more important than immediately knowing the answer.

---

# Question 1

## A Pod is stuck in `Pending`. How would you troubleshoot it?

### Expected Answer

I follow a structured process:

1. Verify the Pod status.

```bash
kubectl get pods
```

2. Describe the Pod.

```bash
kubectl describe pod <pod-name>
```

3. Review Events.

4. Check:

* Resource requests
* NodeSelector
* Taints
* Tolerations
* Affinity
* Available nodes

5. Describe candidate nodes.

```bash
kubectl describe node <node-name>
```

6. Apply the required fix and verify the Pod schedules successfully.

---

# Question 2

## A Pod is in `CrashLoopBackOff`. What would you do?

### Expected Answer

First, I determine why the container is restarting.

Commands:

```bash
kubectl describe pod <pod-name>

kubectl logs <pod-name>

kubectl logs <pod-name> --previous
```

Common causes:

* Application crash
* Invalid configuration
* Missing Secret
* Missing ConfigMap
* Database unavailable
* OOMKilled

I identify the root cause before restarting or redeploying.

---

# Question 3

## A Service is not reachable.

### Expected Answer

I verify:

```bash
kubectl get svc

kubectl describe svc <service-name>

kubectl get endpoints

kubectl get endpointslice
```

Then I confirm:

* Pod labels
* Service selector
* Pod readiness
* NetworkPolicy
* DNS resolution

A Service without Endpoints is one of the most common causes.

---

# Question 4

## DNS resolution is failing inside the cluster.

### Expected Answer

I check:

```bash
kubectl get pods -n kube-system

kubectl logs deployment/coredns -n kube-system
```

Then I launch a temporary Pod:

```bash
kubectl run dns-test \
--image=busybox:1.36 \
-it --rm --restart=Never -- sh
```

Inside:

```sh
nslookup kubernetes.default
```

I also verify `/etc/resolv.conf` inside the Pod.

---

# Question 5

## A PVC remains in `Pending`.

### Expected Answer

Commands:

```bash
kubectl get pvc

kubectl describe pvc <pvc-name>

kubectl get pv

kubectl get sc
```

I verify:

* StorageClass
* Capacity
* Access modes
* Binding status
* CSI driver health

---

# Question 6

## A node reports `NotReady`.

### Expected Answer

Commands:

```bash
kubectl get nodes

kubectl describe node <node-name>
```

On the node:

```bash
systemctl status kubelet

journalctl -u kubelet -n 100
```

I also verify:

* Container runtime
* Disk pressure
* Memory pressure
* Network connectivity
* kubelet configuration

---

# Question 7

## A user receives `Forbidden` when using kubectl.

### Expected Answer

I verify permissions first.

```bash
kubectl auth can-i get pods \
--as=<user>
```

Then I inspect:

```bash
kubectl get roles

kubectl get rolebindings

kubectl get clusterroles

kubectl get clusterrolebindings
```

Finally, I identify which RBAC rule is missing.

---

# Question 8

## How do you troubleshoot a failed Deployment rollout?

### Expected Answer

Commands:

```bash
kubectl rollout status deployment/<deployment>

kubectl describe deployment <deployment>

kubectl get rs

kubectl get pods
```

Possible causes:

* Image pull failure
* Readiness probe failure
* CrashLoopBackOff
* Scheduling issue

---

# Question 9

## How do you troubleshoot a Kubernetes cluster after maintenance?

### Expected Answer

I verify:

```bash
kubectl cluster-info

kubectl get nodes

kubectl get pods -A

kubectl get pods -n kube-system

kubectl get events --sort-by=.lastTimestamp
```

Then I confirm:

* API Server healthy
* Nodes Ready
* CoreDNS healthy
* Applications running

---

# Question 10

## What is your Kubernetes troubleshooting methodology?

### Expected Answer

I follow the same process every time:

1. Understand the reported problem.
2. Identify the affected resource.
3. Run `kubectl get`.
4. Run `kubectl describe`.
5. Review Events.
6. Review Logs.
7. Check dependencies.
8. Identify the root cause.
9. Apply the smallest required fix.
10. Verify the solution.

I avoid making multiple changes at once because it becomes difficult to determine which change resolved the issue.

---

# Question 11

## How do you troubleshoot a control plane issue?

### Expected Answer

I check:

```bash
kubectl cluster-info

kubectl get pods -n kube-system
```

Then I inspect:

* kube-apiserver
* etcd
* kube-controller-manager
* kube-scheduler
* kubelet logs
* Static Pod manifests

If `kubectl` is unavailable, I move directly to the control plane node and inspect the static Pod manifests and kubelet service.

---

# Question 12

## What is the biggest mistake engineers make during troubleshooting?

### Expected Answer

The biggest mistake is changing configuration before collecting evidence.

I always:

* Observe
* Collect information
* Review Events
* Review Logs
* Identify the root cause
* Apply one change
* Verify the result

This minimizes risk and reduces the chance of introducing new problems.

---

# Rapid Fire Questions

| Question                               | Short Answer                |
| -------------------------------------- | --------------------------- |
| First command for troubleshooting?     | `kubectl get`               |
| Best command for root cause?           | `kubectl describe`          |
| Where do scheduling errors appear?     | Events                      |
| Best command for application failures? | `kubectl logs`              |
| Previous container logs?               | `kubectl logs --previous`   |
| Service not working?                   | Check Endpoints             |
| DNS issue?                             | Check CoreDNS               |
| PVC Pending?                           | Check PVC, PV, StorageClass |
| Node NotReady?                         | Check kubelet               |
| Permission denied?                     | `kubectl auth can-i`        |

---

# Senior Interview Tips

Senior interviewers expect you to:

* Follow a repeatable troubleshooting process.
* Explain *why* you run each command.
* Use Events before making changes.
* Use Logs to confirm application failures.
* Understand dependencies between Kubernetes resources.
* Verify every fix before closing the incident.

---

# Final Interview Advice

A strong Kubernetes engineer doesn't guess.

They:

1. Observe.
2. Collect evidence.
3. Identify the root cause.
4. Apply the minimum required fix.
5. Verify the solution.

This disciplined approach is exactly what interviewers—and production environments—expect.
