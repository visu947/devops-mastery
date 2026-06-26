# Cluster Architecture - Commands

## Cluster Information

```bash
kubectl cluster-info
kubectl get nodes
kubectl get nodes -o wide
kubectl get pods -A
kubectl get pods -n kube-system
```

API Discovery
```bash
kubectl api-resources
kubectl api-versions
kubectl explain pod
kubectl explain pod.spec
kubectl explain deployment
```

Control Plane Components
```bash
kubectl get pods -n kube-system
kubectl describe pod kube-apiserver-controlplane -n kube-system
kubectl describe pod kube-scheduler-controlplane -n kube-system
kubectl describe pod kube-controller-manager-controlplane -n kube-system
kubectl describe pod etcd-controlplane -n kube-system
```

Static Pod Manifests
``` bash
ls -l /etc/kubernetes/manifests/
cat /etc/kubernetes/manifests/kube-apiserver.yaml
cat /etc/kubernetes/manifests/kube-scheduler.yaml
cat /etc/kubernetes/manifests/kube-controller-manager.yaml
cat /etc/kubernetes/manifests/etcd.yaml
```

Node Troubleshooting
``` bash
kubectl describe node <node-name>
systemctl status kubelet
journalctl -u kubelet
journalctl -u kubelet -f
```

etcd Snapshot
```bash
ETCDCTL_API=3 etcdctl snapshot save backup.db
```

Useful Alias
```bash
alias k=kubectl
```
