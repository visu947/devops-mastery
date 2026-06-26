# Lab 01 - Explore the Cluster

## Objective

Explore the Kubernetes cluster and verify its basic health.

---

## Commands

```bash
kubectl cluster-info
kubectl get nodes
kubectl get nodes -o wide
kubectl version
```

---

## Expected Output

- Control Plane endpoint
- Worker Node information
- Kubernetes version

---

## Explanation

These commands help you verify:

- Cluster connectivity
- Node availability
- Kubernetes version
- Basic cluster health

---

## CKA Exam Tip

Before troubleshooting any issue, always verify:

- Cluster connectivity
- Node status
- Kubernetes version
