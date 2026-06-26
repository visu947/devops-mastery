# Services & Networking - Commands

> **Quick reference for Kubernetes Services and Networking commands used in the CKA exam, production environments, and technical interviews.**

---

# Table of Contents

1. Services
2. ClusterIP
3. NodePort
4. LoadBalancer
5. ExternalName
6. Endpoints
7. EndpointSlice
8. DNS
9. CoreDNS
10. Ingress
11. Network Policies
12. Troubleshooting
13. Useful Output Formats
14. Explain Resources
15. Production Tips

---

# 1. Services

## List Services

```bash
kubectl get services
```

Short form:

```bash
kubectl get svc
```

---

## Describe a Service

```bash
kubectl describe service <service-name>
```

Short form:

```bash
kubectl describe svc <service-name>
```

---

## View Service YAML

```bash
kubectl get service <service-name> -o yaml
```

---

## View Service Wide Output

```bash
kubectl get svc -o wide
```

---

# 2. ClusterIP

## Create a ClusterIP Service

```bash
kubectl expose pod nginx \
  --name=nginx-service \
  --port=80 \
  --target-port=80
```

---

## Verify ClusterIP

```bash
kubectl get svc
```

---

# 3. NodePort

## Create a NodePort Service

```bash
kubectl expose deployment nginx \
  --type=NodePort \
  --port=80
```

---

## View Assigned NodePort

```bash
kubectl get svc
```

---

## Describe NodePort Service

```bash
kubectl describe svc <service-name>
```

---

# 4. LoadBalancer

## Create a LoadBalancer Service

```bash
kubectl expose deployment nginx \
  --type=LoadBalancer \
  --port=80
```

---

## Verify External IP

```bash
kubectl get svc
```

---

# 5. ExternalName

## View ExternalName Service

```bash
kubectl get svc
```

---

## Describe ExternalName

```bash
kubectl describe svc <service-name>
```

---

# 6. Endpoints

## List Endpoints

```bash
kubectl get endpoints
```

Short form:

```bash
kubectl get ep
```

---

## Describe Endpoints

```bash
kubectl describe endpoints <service-name>
```

---

## View Endpoint YAML

```bash
kubectl get endpoints <service-name> -o yaml
```

---

# 7. EndpointSlice

## List EndpointSlices

```bash
kubectl get endpointslice
```

---

## Describe EndpointSlice

```bash
kubectl describe endpointslice <name>
```

---

## View EndpointSlice YAML

```bash
kubectl get endpointslice <name> -o yaml
```

---

# 8. DNS

## Create a Temporary Test Pod

```bash
kubectl run dns-test \
  --image=busybox:1.36 \
  --restart=Never \
  -it --rm -- sh
```

---

## Test Service DNS

```bash
nslookup <service-name>
```

---

## Test Fully Qualified Domain Name (FQDN)

```bash
nslookup <service-name>.<namespace>.svc.cluster.local
```

---

## Test Connectivity

```bash
wget -qO- http://<service-name>
```

or

```bash
curl http://<service-name>
```

---

# 9. CoreDNS

## Verify CoreDNS Pods

```bash
kubectl get pods -n kube-system
```

---

## Describe CoreDNS

```bash
kubectl describe deployment coredns -n kube-system
```

---

## View CoreDNS Logs

```bash
kubectl logs -n kube-system deployment/coredns
```

---

## View CoreDNS ConfigMap

```bash
kubectl get configmap coredns -n kube-system -o yaml
```

---

# 10. Ingress

## List Ingress Resources

```bash
kubectl get ingress
```

Short form:

```bash
kubectl get ing
```

---

## Describe Ingress

```bash
kubectl describe ingress <ingress-name>
```

---

## View Ingress YAML

```bash
kubectl get ingress <ingress-name> -o yaml
```

---

## Verify Ingress Controller

```bash
kubectl get pods -n ingress-nginx
```

---

# 11. Network Policies

## List Network Policies

```bash
kubectl get networkpolicy
```

Short form:

```bash
kubectl get netpol
```

---

## Describe Network Policy

```bash
kubectl describe networkpolicy <policy-name>
```

---

## View Network Policy YAML

```bash
kubectl get networkpolicy <policy-name> -o yaml
```

---

# 12. Troubleshooting

## Verify Pods

```bash
kubectl get pods -o wide
```

---

## Verify Services

```bash
kubectl get svc
```

---

## Verify Endpoints

```bash
kubectl get endpoints
```

---

## Verify EndpointSlices

```bash
kubectl get endpointslice
```

---

## View Events

```bash
kubectl get events --sort-by=.lastTimestamp
```

---

## Describe Pod

```bash
kubectl describe pod <pod-name>
```

---

## Test Service Connectivity

```bash
kubectl exec -it <pod-name> -- sh
```

Inside the Pod:

```bash
nslookup <service-name>

wget -qO- http://<service-name>
```

---

# 13. Useful Output Formats

## Service YAML

```bash
kubectl get svc <service-name> -o yaml
```

---

## Endpoint YAML

```bash
kubectl get endpoints <service-name> -o yaml
```

---

## EndpointSlice YAML

```bash
kubectl get endpointslice <name> -o yaml
```

---

## Ingress YAML

```bash
kubectl get ingress <ingress-name> -o yaml
```

---

# 14. Explain Resources

## Service

```bash
kubectl explain service
```

---

## Service Spec

```bash
kubectl explain service.spec
```

---

## Ingress

```bash
kubectl explain ingress
```

---

## NetworkPolicy

```bash
kubectl explain networkpolicy
```

---

## EndpointSlice

```bash
kubectl explain endpointslice
```

---

# 15. Most Common Networking Troubleshooting Commands

```bash
kubectl get pods -o wide

kubectl get svc

kubectl get endpoints

kubectl get endpointslice

kubectl describe svc <service-name>

kubectl describe pod <pod-name>

kubectl get events --sort-by=.lastTimestamp

kubectl exec -it <pod-name> -- sh

nslookup <service-name>

wget -qO- http://<service-name>
```

---

# Production Tips

✅ Verify Pods are Ready before troubleshooting Services.

✅ If a Service has **no Endpoints**, check the selector labels.

✅ Use DNS names instead of Pod IP addresses.

✅ Verify the Ingress Controller is running before debugging Ingress resources.

✅ Use `kubectl exec` to test networking from inside the cluster.

✅ Check Network Policies when traffic is unexpectedly blocked.

---

# CKA Exam Tips

✔ Service not working?

Check in this order:

1. Pods
2. Labels
3. Service selector
4. Endpoints
5. DNS
6. Ingress
7. NetworkPolicy

✔ Empty Endpoints usually indicate a selector mismatch.

✔ Always test DNS before assuming a networking issue.

---

# References

* Kubernetes Services Documentation
* Kubernetes DNS Documentation
* CoreDNS Documentation
* Ingress Documentation
* Network Policies Documentation
