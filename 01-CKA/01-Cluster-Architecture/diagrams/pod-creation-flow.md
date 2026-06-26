
---

## 2️⃣ pod-creation-flow.md

```markdown
# Pod Creation Flow

## Workflow

```text
Developer

↓

kubectl apply

↓

API Server

↓

Authentication

↓

Authorization

↓

Admission Controllers

↓

etcd

↓

Deployment Controller

↓

ReplicaSet

↓

Scheduler

↓

Worker Node

↓

kubelet

↓

containerd

↓

Pod Running
