# Troubleshooting - Commands

> **Quick reference for Kubernetes troubleshooting commands used in the CKA exam and production environments.**

---

# Table of Contents

1. General Troubleshooting
2. Pod Troubleshooting
3. Container Logs
4. Events
5. Deployment Troubleshooting
6. Scheduling Troubleshooting
7. Node Troubleshooting
8. Networking Troubleshooting
9. DNS Troubleshooting
10. Storage Troubleshooting
11. Security Troubleshooting
12. Control Plane Troubleshooting
13. Resource Usage
14. Useful Output Formats

---

# 1. General Troubleshooting

## View All Resources

```bash
kubectl get all
```

```bash
kubectl get all -A
```

---

## View Cluster Information

```bash
kubectl cluster-info
```

---

## View Cluster Version

```bash
kubectl version
```

---

## View Current Context

```bash
kubectl config current-context
```

---

## View Events

```bash
kubectl get events --sort-by=.lastTimestamp
```

---

# 2. Pod Troubleshooting

## List Pods

```bash
kubectl get pods
```

```bash
kubectl get pods -A
```

```bash
kubectl get pods -o wide
```

---

## Describe Pod

```bash
kubectl describe pod <pod-name>
```

---

## View Pod YAML

```bash
kubectl get pod <pod-name> -o yaml
```

---

## Watch Pod Status

```bash
kubectl get pods -w
```

---

## Check Pod Labels

```bash
kubectl get pods --show-labels
```

---

## Execute Into Pod

```bash
kubectl exec -it <pod-name> -- sh
```

---

## Execute Into Specific Container

```bash
kubectl exec -it <pod-name> -c <container-name> -- sh
```

---

# 3. Container Logs

## View Logs

```bash
kubectl logs <pod-name>
```

---

## View Logs for a Specific Container

```bash
kubectl logs <pod-name> -c <container-name>
```

---

## Follow Logs

```bash
kubectl logs -f <pod-name>
```

---

## View Previous Container Logs

Useful for `CrashLoopBackOff`:

```bash
kubectl logs <pod-name> --previous
```

---

## Logs by Label

```bash
kubectl logs -l app=<label-value>
```

---

# 4. Events

## View Events Sorted by Time

```bash
kubectl get events --sort-by=.lastTimestamp
```

---

## View Events in a Namespace

```bash
kubectl get events -n <namespace>
```

---

## Watch Events

```bash
kubectl get events -w
```

---

# 5. Deployment Troubleshooting

## List Deployments

```bash
kubectl get deployments
```

---

## Describe Deployment

```bash
kubectl describe deployment <deployment-name>
```

---

## View ReplicaSets

```bash
kubectl get rs
```

---

## Rollout Status

```bash
kubectl rollout status deployment/<deployment-name>
```

---

## Rollout History

```bash
kubectl rollout history deployment/<deployment-name>
```

---

## Undo Rollout

```bash
kubectl rollout undo deployment/<deployment-name>
```

---

## Restart Deployment

```bash
kubectl rollout restart deployment/<deployment-name>
```

---

## Scale Deployment

```bash
kubectl scale deployment <deployment-name> --replicas=<count>
```

---

# 6. Scheduling Troubleshooting

## Check Pending Pods

```bash
kubectl get pods
```

```bash
kubectl describe pod <pod-name>
```

---

## View Nodes

```bash
kubectl get nodes
```

```bash
kubectl get nodes -o wide
```

---

## Describe Node

```bash
kubectl describe node <node-name>
```

---

## Check Node Labels

```bash
kubectl get nodes --show-labels
```

---

## Check Taints

```bash
kubectl describe node <node-name> | grep -i taint
```

---

## Check Pod Scheduling Rules

```bash
kubectl get pod <pod-name> -o yaml
```

Look for:

* `nodeSelector`
* `affinity`
* `tolerations`
* `resources.requests`

---

# 7. Node Troubleshooting

## List Nodes

```bash
kubectl get nodes
```

---

## Describe Node

```bash
kubectl describe node <node-name>
```

---

## Check Node Conditions

```bash
kubectl describe node <node-name>
```

Look for:

* Ready
* MemoryPressure
* DiskPressure
* PIDPressure
* NetworkUnavailable

---

## Check kubelet Status

Run on the node:

```bash
systemctl status kubelet
```

---

## View kubelet Logs

```bash
journalctl -u kubelet -n 100
```

```bash
journalctl -u kubelet -f
```

---

## Restart kubelet

```bash
sudo systemctl restart kubelet
```

---

# 8. Networking Troubleshooting

## List Services

```bash
kubectl get svc
```

```bash
kubectl get svc -A
```

---

## Describe Service

```bash
kubectl describe svc <service-name>
```

---

## Check Endpoints

```bash
kubectl get endpoints
```

```bash
kubectl get endpoints <service-name>
```

---

## Check EndpointSlices

```bash
kubectl get endpointslice
```

---

## Check Pod Labels

```bash
kubectl get pods --show-labels
```

---

## Test Service from Temporary Pod

```bash
kubectl run net-test \
  --image=busybox:1.36 \
  --restart=Never \
  -it --rm -- sh
```

Inside the Pod:

```sh
wget -qO- http://<service-name>
```

---

## Port Forward Service

```bash
kubectl port-forward svc/<service-name> 8080:<service-port>
```

---

# 9. DNS Troubleshooting

## Test DNS Resolution

```bash
kubectl run dns-test \
  --image=busybox:1.36 \
  --restart=Never \
  -it --rm -- sh
```

Inside the Pod:

```sh
nslookup <service-name>
```

```sh
nslookup <service-name>.<namespace>.svc.cluster.local
```

---

## Check CoreDNS Pods

```bash
kubectl get pods -n kube-system
```

---

## View CoreDNS Logs

```bash
kubectl logs -n kube-system deployment/coredns
```

---

## Check DNS Service

```bash
kubectl get svc -n kube-system
```

Look for:

```text
kube-dns
```

---

## Inspect Pod Resolver Config

Inside a Pod:

```sh
cat /etc/resolv.conf
```

---

# 10. Storage Troubleshooting

## List PVCs

```bash
kubectl get pvc
```

---

## Describe PVC

```bash
kubectl describe pvc <pvc-name>
```

---

## List PVs

```bash
kubectl get pv
```

---

## Describe PV

```bash
kubectl describe pv <pv-name>
```

---

## List StorageClasses

```bash
kubectl get sc
```

---

## Check CSI Drivers

```bash
kubectl get csidriver
```

---

## Check CSI Nodes

```bash
kubectl get csinode
```

---

## Inspect Pod Mounts

```bash
kubectl exec -it <pod-name> -- df -h
```

```bash
kubectl exec -it <pod-name> -- mount
```

---

# 11. Security Troubleshooting

## Check Permissions

```bash
kubectl auth can-i get pods
```

```bash
kubectl auth can-i create deployments
```

---

## Check Permissions as ServiceAccount

```bash
kubectl auth can-i get pods \
--as=system:serviceaccount:<namespace>:<serviceaccount>
```

---

## List ServiceAccounts

```bash
kubectl get sa
```

---

## Describe ServiceAccount

```bash
kubectl describe sa <serviceaccount-name>
```

---

## List RBAC Resources

```bash
kubectl get roles
```

```bash
kubectl get rolebindings
```

```bash
kubectl get clusterroles
```

```bash
kubectl get clusterrolebindings
```

---

## List Secrets

```bash
kubectl get secrets
```

---

## Decode Secret

```bash
kubectl get secret <secret-name> \
-o jsonpath='{.data.<key>}' | base64 --decode
```

---

# 12. Control Plane Troubleshooting

## Check kube-system Pods

```bash
kubectl get pods -n kube-system
```

---

## Describe kube-system Pod

```bash
kubectl describe pod <pod-name> -n kube-system
```

---

## View Control Plane Static Pod Manifests

On control plane node:

```bash
ls -l /etc/kubernetes/manifests
```

---

## Check API Server

```bash
kubectl cluster-info
```

---

## Check etcd Pod

```bash
kubectl get pods -n kube-system | grep etcd
```

---

## Check kubelet

```bash
systemctl status kubelet
```

```bash
journalctl -u kubelet -n 100
```

---

# 13. Resource Usage

## View Node Metrics

```bash
kubectl top nodes
```

---

## View Pod Metrics

```bash
kubectl top pods
```

---

## View Pod Metrics in All Namespaces

```bash
kubectl top pods -A
```

> `kubectl top` requires Metrics Server.

---

# 14. Useful Output Formats

## YAML

```bash
kubectl get <resource> <name> -o yaml
```

---

## JSON

```bash
kubectl get <resource> <name> -o json
```

---

## Wide Output

```bash
kubectl get pods -o wide
```

---

## Custom Columns

```bash
kubectl get pods \
-o custom-columns=NAME:.metadata.name,NODE:.spec.nodeName,STATUS:.status.phase
```

---

# Most Important Troubleshooting Commands

```bash
kubectl get pods -A

kubectl describe pod <pod-name>

kubectl logs <pod-name>

kubectl logs <pod-name> --previous

kubectl get events --sort-by=.lastTimestamp

kubectl get nodes

kubectl describe node <node-name>

kubectl get svc

kubectl get endpoints

kubectl get pvc

kubectl describe pvc <pvc-name>

kubectl auth can-i get pods

kubectl cluster-info
```

---

# CKA Exam Tips

* Start with `kubectl get`.
* Use `kubectl describe` to inspect events.
* Use `kubectl logs --previous` for restarted containers.
* Check selectors and labels for Service issues.
* Check PVC status before debugging storage mounts.
* Check `kubectl auth can-i` for RBAC problems.
* Always verify the fix after making changes.
