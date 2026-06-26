# Services & Networking - Troubleshooting Guide

> A production-focused guide for diagnosing and resolving Kubernetes Services and Networking issues.

---

# Networking Troubleshooting Workflow

Always troubleshoot networking problems in the following order:

```text
Application Unreachable
        │
        ▼
Pods Running?
        │
        ▼
Pods Ready?
        │
        ▼
Service Exists?
        │
        ▼
Service Selector
        │
        ▼
Endpoints / EndpointSlices
        │
        ▼
DNS Resolution
        │
        ▼
Ingress
        │
        ▼
NetworkPolicy
        │
        ▼
kube-proxy
        │
        ▼
Root Cause
```

Never begin by modifying YAML. First determine where traffic stops.

---

# Scenario 1 - Service Not Reachable

## Symptoms

* Application unavailable
* Connection refused
* Timeout

---

## Investigation

```bash
kubectl get pods -o wide

kubectl get svc

kubectl describe svc <service-name>

kubectl get endpoints
```

---

## Possible Causes

* Service missing
* Wrong selector
* No backend Pods
* Pods not Ready

---

## Resolution

* Verify selector labels.
* Verify Pods are Ready.
* Verify Endpoints exist.

---

## Prevention

Keep Service selectors simple and consistent.

---

# Scenario 2 - Service Has No Endpoints

## Symptoms

```text
ENDPOINTS

<none>
```

---

## Investigation

```bash
kubectl get svc

kubectl get endpoints

kubectl get pods --show-labels

kubectl describe svc <service-name>
```

---

## Root Cause

Service selector does not match Pod labels.

Example:

Service:

```yaml
selector:
  app: backend
```

Pods:

```yaml
labels:
  app: api
```

---

## Resolution

Update either:

* Service selector

or

* Pod labels

---

# Scenario 3 - Pods Running But No Traffic

## Symptoms

Pods:

```text
Running
```

Application:

Unavailable

---

## Investigation

```bash
kubectl get pods

kubectl describe pod <pod-name>
```

Verify:

```text
READY
```

---

## Root Cause

Pods are Running but **not Ready**.

Services only send traffic to Ready Pods.

---

## Resolution

Fix:

* Readiness probe
* Application startup
* Dependency failures

---

# Scenario 4 - DNS Resolution Failure

## Symptoms

```text
nslookup backend

fails
```

---

## Investigation

```bash
kubectl exec -it <pod-name> -- sh

nslookup kubernetes.default

nslookup <service-name>

kubectl get pods -n kube-system

kubectl logs -n kube-system deployment/coredns
```

---

## Possible Causes

* CoreDNS unavailable
* Service missing
* NetworkPolicy blocking DNS
* Incorrect namespace

---

## Resolution

Restore CoreDNS or correct the DNS configuration.

---

# Scenario 5 - Ingress Returns 404

## Symptoms

```text
HTTP 404
```

---

## Investigation

```bash
kubectl get ingress

kubectl describe ingress

kubectl get svc

kubectl get endpoints
```

---

## Possible Causes

* Wrong host
* Wrong path
* Missing backend Service
* Empty Endpoints

---

## Resolution

Verify:

* Ingress rules
* Backend Service
* Endpoints
* Ingress Controller

---

# Scenario 6 - Ingress Does Not Respond

## Symptoms

No response from external URL.

---

## Investigation

```bash
kubectl get pods -n ingress-nginx

kubectl get svc -n ingress-nginx

kubectl describe ingress
```

---

## Root Cause

Ingress Controller not installed or unhealthy.

---

## Resolution

Deploy or restore the Ingress Controller.

---

# Scenario 7 - NetworkPolicy Blocking Traffic

## Symptoms

Pods are healthy.

Services are healthy.

Traffic fails.

---

## Investigation

```bash
kubectl get networkpolicy

kubectl describe networkpolicy
```

---

## Root Cause

Traffic is denied by NetworkPolicy rules.

---

## Resolution

Update ingress and egress rules to allow the required traffic.

---

# Scenario 8 - ExternalName Service Not Working

## Symptoms

Application cannot reach an external system.

---

## Investigation

```bash
kubectl describe svc <service-name>

nslookup <external-host>
```

---

## Root Cause

* Incorrect external DNS name
* External DNS unavailable

---

## Resolution

Verify the external hostname and external DNS connectivity.

---

# Scenario 9 - NodePort Not Accessible

## Symptoms

```text
Connection timed out
```

---

## Investigation

```bash
kubectl get svc

kubectl describe svc

kubectl get nodes -o wide
```

---

## Possible Causes

* Incorrect NodePort
* Firewall restrictions
* Service missing
* Backend Pods unavailable

---

## Resolution

Verify the NodePort, node IP, firewall rules, and Service configuration.

---

# Scenario 10 - LoadBalancer EXTERNAL-IP Pending

## Symptoms

```text
EXTERNAL-IP

<pending>
```

---

## Investigation

```bash
kubectl get svc
```

Determine whether the cluster is running:

* Cloud provider
* Minikube
* Kind
* Docker Desktop

---

## Root Cause

The cluster does not provide a cloud LoadBalancer implementation.

---

## Resolution

* Use a supported cloud provider.
* Install MetalLB for local clusters.
* Use NodePort for testing if appropriate.

---

# Scenario 11 - kube-proxy Problems

## Symptoms

Services exist.

Endpoints exist.

Traffic still fails.

---

## Investigation

```bash
kubectl get pods -n kube-system

kubectl logs -n kube-system daemonset/kube-proxy
```

---

## Root Cause

kube-proxy is not programming the required networking rules.

---

## Resolution

Restore kube-proxy and verify node networking.

---

# Scenario 12 - Cross-Namespace Service Access

## Symptoms

Service lookup fails.

Application uses:

```text
backend
```

Service exists in another namespace.

---

## Investigation

```bash
kubectl get svc -A
```

---

## Resolution

Use the fully qualified DNS name:

```text
backend.production.svc.cluster.local
```

---

# Production Debugging Checklist

Always verify:

## Pods

```bash
kubectl get pods -o wide
```

---

## Services

```bash
kubectl get svc
```

---

## Endpoints

```bash
kubectl get endpoints
```

---

## EndpointSlices

```bash
kubectl get endpointslice
```

---

## DNS

```bash
kubectl exec -it <pod> -- sh

nslookup kubernetes.default

nslookup <service-name>
```

---

## Ingress

```bash
kubectl get ingress

kubectl describe ingress
```

---

## Network Policies

```bash
kubectl get networkpolicy
```

---

## Events

```bash
kubectl get events --sort-by=.lastTimestamp
```

---

# Common Networking Errors

| Symptom                          | Likely Cause                |
| -------------------------------- | --------------------------- |
| Service has no Endpoints         | Selector mismatch           |
| DNS lookup fails                 | CoreDNS or namespace issue  |
| 404 from Ingress                 | Incorrect rule or backend   |
| EXTERNAL-IP Pending              | No cloud LoadBalancer       |
| Service unreachable              | Pods not Ready              |
| Traffic blocked                  | NetworkPolicy               |
| NodePort timeout                 | Firewall or node networking |
| Service exists but traffic fails | kube-proxy issue            |

---

# Production Tips

* Verify Pods before troubleshooting Services.
* Verify Ready status, not just Running status.
* Check Endpoints before debugging DNS.
* Use DNS names instead of Pod IPs.
* Confirm the Ingress Controller is healthy.
* Test connectivity from inside the cluster before testing externally.
* Review NetworkPolicies whenever traffic unexpectedly stops.

---

# Interviewer's Perspective

A common senior interview question is:

> "Users report that a Kubernetes application is unavailable. Walk me through your troubleshooting process."

A strong answer includes:

1. Verify Pods are Running and Ready.
2. Verify the Service exists.
3. Check Service selectors.
4. Verify Endpoints or EndpointSlices.
5. Test DNS resolution.
6. Verify Ingress configuration and controller.
7. Review NetworkPolicies.
8. Check kube-proxy if Service routing still fails.
9. Identify the root cause.
10. Apply the smallest safe fix and verify recovery.

This structured approach demonstrates production-ready troubleshooting skills rather than relying on trial-and-error.
