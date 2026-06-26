
## 2️⃣ pod-creation-flow.md

# Pod Creation Flow

## Workflow

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


## Summary
User submits request.
API Server validates.
Desired state stored in etcd.
Controller creates ReplicaSet.
Scheduler assigns node.
kubelet starts Pod.
Runtime launches container.
kubelet reports status.
