# Pods - Common Mistakes

This document highlights common misconceptions about Kubernetes Pods that frequently appear in the CKA exam and technical interviews.

---

# Mistake 1

## A Pod is the same as a Container.

❌ Incorrect

A Pod is an abstraction that contains one or more containers.

A container cannot exist in Kubernetes without being part of a Pod.

---

# Mistake 2

## Pods should be permanent.

❌ Incorrect

Pods are ephemeral.

They can be terminated and recreated at any time.

Applications should not rely on Pod IP addresses or local filesystem storage.

---

# Mistake 3

## Every Pod should contain multiple containers.

❌ Incorrect

The majority of Pods contain a single application container.

Use multiple containers only when they are tightly coupled.

Examples:

- Sidecar
- Adapter
- Ambassador

---

# Mistake 4

## Standalone Pods should be used in production.

❌ Incorrect

Production workloads should generally be managed using:

- Deployment
- StatefulSet
- DaemonSet
- Job
- CronJob

These controllers provide self-healing and lifecycle management.

---

# Mistake 5

## CrashLoopBackOff is a Pod phase.

❌ Incorrect

CrashLoopBackOff is a container waiting state.

Pod phases are:

- Pending
- Running
- Succeeded
- Failed
- Unknown

---

# Mistake 6

## ImagePullBackOff means Kubernetes is broken.

❌ Incorrect

Common causes include:

- Wrong image name
- Wrong tag
- Missing registry credentials
- Network connectivity issues

---

# Mistake 7

## Resource limits are optional in production.

❌ Incorrect

Without requests and limits:

- Scheduling becomes inefficient.
- Pods may consume excessive resources.
- The risk of eviction increases.

---

# Mistake 8

## Pods keep the same IP forever.

❌ Incorrect

When a Pod is recreated:

- New UID
- New IP
- Potentially a different Worker Node

Applications should rely on Kubernetes Services instead of Pod IPs.

---

# Mistake 9

## Init Containers run continuously.

❌ Incorrect

Init Containers run once and exit successfully before application containers start.

---

# Mistake 10

## Sidecar Containers exit after startup.

❌ Incorrect

Sidecar Containers continue running alongside the main application for the lifetime of the Pod.

---

# Key Takeaways

- Pods are ephemeral.
- Pods are scheduled as a unit.
- Containers inside a Pod share networking.
- Standalone Pods are rarely used in production.
- Use Deployments for long-running applications.
