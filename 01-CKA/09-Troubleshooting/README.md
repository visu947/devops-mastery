# CKA - Troubleshooting

> **Goal:** Learn a systematic approach to diagnosing and resolving Kubernetes issues in production and during the CKA exam.

---

# 📚 Chapter Contents

* Learning Objectives
* Why Troubleshooting Matters
* Kubernetes Troubleshooting Workflow
* Common Failure Domains
* Troubleshooting Methodology
* Production Best Practices
* Summary
* References

---

# Learning Objectives

After completing this chapter, you will be able to:

* Troubleshoot Pods, Deployments, and StatefulSets.
* Diagnose scheduling failures.
* Troubleshoot Services, DNS, and Ingress.
* Resolve PersistentVolume and PersistentVolumeClaim issues.
* Troubleshoot RBAC, ServiceAccounts, Secrets, and certificates.
* Investigate node and control plane failures.
* Use logs, events, and resource descriptions effectively.
* Apply a structured troubleshooting methodology for production incidents and the CKA exam.

---

# Why Troubleshooting Matters

Creating Kubernetes resources is only one part of cluster administration.

In production, administrators spend much of their time:

* Diagnosing failed deployments
* Recovering applications
* Investigating node failures
* Resolving networking problems
* Fixing storage issues
* Recovering from maintenance failures

The CKA exam also emphasizes **finding and fixing problems**, not just creating resources.

---

# Kubernetes Troubleshooting Workflow

```text
User Reports Issue

↓

Identify Affected Resource

↓

Describe Resource

↓

Check Events

↓

Check Logs

↓

Check Dependencies

↓

Identify Root Cause

↓

Apply Fix

↓

Verify Solution
```

This workflow can be applied to nearly every Kubernetes issue.

---

# Common Failure Domains

This chapter covers troubleshooting across all major Kubernetes domains.

## Workloads

* Pods
* ReplicaSets
* Deployments
* StatefulSets
* Jobs
* CronJobs

---

## Scheduling

* NodeSelector
* Taints
* Tolerations
* Affinity
* Resource requests
* Resource limits

---

## Networking

* Services
* ClusterIP
* NodePort
* LoadBalancer
* Ingress
* DNS
* EndpointSlice
* NetworkPolicy

---

## Storage

* Volumes
* PersistentVolumes
* PersistentVolumeClaims
* StorageClasses
* CSI Drivers

---

## Security

* ServiceAccounts
* RBAC
* Secrets
* Security Context
* Pod Security Admission
* Certificates

---

## Cluster Components

* kubelet
* API Server
* etcd
* Scheduler
* Controller Manager
* kube-proxy

---

# Troubleshooting Methodology

Instead of memorizing fixes, use a consistent process.

## Step 1 — Verify the Problem

Ask:

* What is failing?
* Is the issue reproducible?
* Is the impact isolated or cluster-wide?

---

## Step 2 — Gather Information

Common commands:

```bash
kubectl get

kubectl describe

kubectl logs

kubectl get events

kubectl top
```

---

## Step 3 — Check Dependencies

Examples:

* Is the node healthy?
* Is the Service selecting the correct Pods?
* Is the PVC bound?
* Is DNS resolving?
* Does RBAC allow access?

---

## Step 4 — Identify the Root Cause

Avoid guessing.

Use evidence from:

* Events
* Logs
* Resource status
* Component health

---

## Step 5 — Apply the Fix

Examples:

* Correct the manifest.
* Restart the affected component.
* Update labels or selectors.
* Restore missing resources.
* Fix permissions.

---

## Step 6 — Verify the Solution

Always confirm:

* Resources are healthy.
* Applications are functioning.
* No new warning events appear.

---

# Production Best Practices

* Start with the simplest explanation.
* Read Events before changing resources.
* Check logs before restarting components.
* Change one thing at a time.
* Verify the fix before moving on.
* Document root causes and resolutions.
* Avoid unnecessary changes during incidents.

---

# Common Troubleshooting Commands

```bash
kubectl get all -A

kubectl describe pod <pod-name>

kubectl logs <pod-name>

kubectl logs <pod-name> -c <container-name>

kubectl get events --sort-by=.lastTimestamp

kubectl top nodes

kubectl top pods

kubectl get nodes

kubectl get pods -A

kubectl cluster-info
```

---

# Chapter Summary

By the end of this chapter, you will be able to troubleshoot:

* Pods
* Workloads
* Scheduling
* Networking
* DNS
* Storage
* Security
* Control Plane
* Nodes
* Cluster Maintenance

using a structured, production-ready methodology.

These troubleshooting skills are among the most valuable competencies for both the **CKA exam** and real-world Kubernetes administration.

---

# Chapter Roadmap

```text
Troubleshooting

│

├── Pod Troubleshooting
│
├── Scheduling Issues
│
├── Networking Issues
│
├── DNS Troubleshooting
│
├── Storage Troubleshooting
│
├── Security Troubleshooting
│
├── Control Plane Troubleshooting
│
├── Node Troubleshooting
│
├── End-to-End Production Incident
│
└── CKA Lightning Scenarios
```

---

# References

* Kubernetes Documentation
* Kubernetes Debugging Guide
* Kubernetes Troubleshooting Tasks
* Certified Kubernetes Administrator (CKA) Curriculum
* Kubernetes Best Practices
