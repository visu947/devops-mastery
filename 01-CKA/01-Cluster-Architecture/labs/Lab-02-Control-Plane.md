# Lab 02 - Control Plane Components

## Objective

Inspect Control Plane Pods.

## Commands

```bash
kubectl get pods -n kube-system
kubectl describe pod kube-apiserver-<node> -n kube-system
```

## Learn

- API Server
- Scheduler
- Controller Manager
- etcd

## Exam Tip

Most kubeadm clusters run Control Plane components as Static Pods.
