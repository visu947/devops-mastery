# Lab 07 - Control Plane Troubleshooting

## Difficulty

⭐⭐⭐⭐⭐ Expert

## Estimated Time

50–70 minutes

---

# CKA Objectives Covered

* Troubleshoot API Server failures
* Diagnose etcd issues
* Investigate kube-scheduler problems
* Troubleshoot kube-controller-manager
* Verify static Pod manifests
* Troubleshoot kubelet on the control plane

---

# Objective

In this lab, you will troubleshoot failures involving Kubernetes control plane components including:

* API Server unavailable
* etcd failures
* kube-scheduler issues
* kube-controller-manager issues
* Static Pod manifest problems
* kubelet failures

Your goal is to restore the Kubernetes control plane to a healthy state.

---

# Architecture

```mermaid id="cp01"
flowchart TD

Kubelet[kubelet]

StaticPods[/etc/kubernetes/manifests]

APIServer[kube-apiserver]

Etcd[(etcd)]

Scheduler[kube-scheduler]

Controller[kube-controller-manager]

Kubelet --> StaticPods
StaticPods --> APIServer
StaticPods --> Etcd
StaticPods --> Scheduler
StaticPods --> Controller
APIServer --> Etcd
```

---

# Control Plane Troubleshooting Workflow

```text id="cp02"
kubectl Fails

↓

Check kubelet

↓

Check Static Pod Manifests

↓

Check API Server

↓

Check etcd

↓

Check Scheduler

↓

Check Controller Manager

↓

Review Logs

↓

Apply Fix

↓

Verify Cluster
```

---

# Scenario 1 - API Server Unavailable

## Symptoms

```text id="cp03"
The connection to the server was refused
```

---

## Investigation

Check cluster information:

```bash id="cp04"
kubectl cluster-info
```

If unavailable, move to the control plane node.

Verify kubelet:

```bash id="cp05"
systemctl status kubelet
```

Review logs:

```bash id="cp06"
journalctl -u kubelet -n 100
```

---

## Resolution

Ensure:

* kubelet is running.
* Static Pod manifests are valid.
* API Server Pod starts successfully.

---

# Scenario 2 - Static Pod Manifest Error

## Investigation

List manifests:

```bash id="cp07"
ls -l /etc/kubernetes/manifests
```

Inspect:

```bash id="cp08"
sudo vi /etc/kubernetes/manifests/kube-apiserver.yaml
```

Check for:

* YAML syntax errors
* Incorrect image
* Invalid volume mounts
* Incorrect command arguments

---

## Resolution

Correct the manifest.

kubelet automatically recreates the static Pod.

---

# Scenario 3 - etcd Failure

## Symptoms

API Server unavailable or repeatedly restarting.

---

## Investigation

Verify etcd Pod:

```bash id="cp09"
kubectl get pods -n kube-system
```

If `kubectl` is unavailable:

```bash id="cp10"
crictl ps -a
```

Review logs:

```bash id="cp11"
crictl logs <container-id>
```

Check endpoint health:

```bash id="cp12"
ETCDCTL_API=3 etcdctl endpoint health \
--endpoints=https://127.0.0.1:2379
```

---

## Resolution

Verify:

* etcd certificates
* Data directory
* Static Pod manifest
* Disk availability

Restore from a snapshot if necessary.

---

# Scenario 4 - kube-scheduler Failure

## Investigation

Verify scheduler Pod:

```bash id="cp13"
kubectl get pods -n kube-system
```

If unavailable:

```bash id="cp14"
crictl ps -a
```

Inspect manifest:

```bash id="cp15"
ls /etc/kubernetes/manifests
```

---

## Resolution

Correct manifest or configuration issues and confirm the scheduler Pod becomes healthy.

---

# Scenario 5 - kube-controller-manager Failure

## Investigation

```bash id="cp16"
kubectl get pods -n kube-system
```

Review logs:

```bash id="cp17"
kubectl logs -n kube-system <controller-manager-pod>
```

If `kubectl` is unavailable:

```bash id="cp18"
crictl logs <container-id>
```

---

## Resolution

Verify:

* Certificates
* Manifest
* Startup arguments

---

# Scenario 6 - kubelet Failure

## Investigation

```bash id="cp19"
systemctl status kubelet

journalctl -u kubelet -n 100
```

Verify:

* Service status
* Configuration
* Static Pod loading
* Container runtime connectivity

---

## Resolution

Restart if appropriate:

```bash id="cp20"
sudo systemctl restart kubelet
```

Investigate configuration errors if it does not recover.

---

# Scenario 7 - Control Plane Certificates

## Investigation

```bash id="cp21"
sudo kubeadm certs check-expiration
```

---

## Resolution

Renew if required:

```bash id="cp22"
sudo kubeadm certs renew all

sudo systemctl restart kubelet
```

---

# Scenario 8 - Control Plane Recovery Verification

Run:

```bash id="cp23"
kubectl cluster-info

kubectl get nodes

kubectl get pods -A

kubectl get pods -n kube-system
```

Verify:

* API Server reachable
* Nodes Ready
* Control plane Pods healthy
* Applications functioning

---

# Useful Commands

```bash id="cp24"
kubectl cluster-info

kubectl get pods -n kube-system

systemctl status kubelet

journalctl -u kubelet -n 100

ls -l /etc/kubernetes/manifests

crictl ps -a

crictl logs <container-id>

sudo kubeadm certs check-expiration

ETCDCTL_API=3 etcdctl endpoint health
```

---

# Verification Checklist

✅ kubelet healthy.

✅ Static Pod manifests valid.

✅ API Server running.

✅ etcd healthy.

✅ Scheduler healthy.

✅ Controller Manager healthy.

✅ Cluster operational.

---

# Common Mistakes

❌ Assuming `kubectl` will always work.

❌ Forgetting that control plane components are static Pods.

❌ Ignoring kubelet when static Pods disappear.

❌ Editing multiple manifest fields at once.

❌ Restarting components without reviewing logs.

---

# Production Discussion

A reliable control plane troubleshooting order is:

```text id="cp25"
kubelet

↓

Static Pod Manifests

↓

API Server

↓

etcd

↓

Scheduler

↓

Controller Manager

↓

Certificates

↓

Cluster Verification
```

This sequence isolates the underlying dependency before moving to higher-level components.

---

# Knowledge Check

1. Where are static Pod manifests stored on a kubeadm cluster?
2. Why might `kubectl` fail even though the node is running?
3. Which component recreates static Pods?
4. How do you verify etcd health?
5. Why is kubelet one of the first components to investigate?

---

# Challenge

A production control plane becomes unavailable.

Investigate the following:

* kubelet status
* Static Pod manifests
* API Server health
* etcd availability
* Scheduler
* Controller Manager
* Certificate expiration

For each issue:

1. Identify the troubleshooting commands.
2. Determine the root cause.
3. Apply the appropriate fix.
4. Verify that the control plane has recovered.
5. Explain why troubleshooting should begin with **kubelet and static Pod manifests** before investigating higher-level Kubernetes components.
