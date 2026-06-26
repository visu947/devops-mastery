# Services & Networking - Common Mistakes

> This document highlights common Kubernetes Services and Networking mistakes seen in the CKA exam, production environments, and technical interviews.

---

# Mistake 1

## Using Pod IPs Instead of Service Names

❌ Incorrect

Applications communicate directly using Pod IP addresses.

### Why This Is a Problem

Pod IPs are temporary.

Whenever a Pod is recreated:

* IP changes
* Applications break

### Correct Approach

Always communicate using the Service DNS name.

Example:

```text
backend.default.svc.cluster.local
```

or simply:

```text
backend
```

---

# Mistake 2

## Service Selector Does Not Match Pod Labels

Example

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

Result:

```text
Endpoints: <none>
```

### Verify

```bash
kubectl get svc

kubectl get endpoints

kubectl get pods --show-labels
```

---

# Mistake 3

## Forgetting That Services Only Route to Ready Pods

Pods may be:

* Running
* Not Ready

If readiness probes fail:

The Service does not send traffic.

Verify:

```bash
kubectl get pods

kubectl describe pod
```

---

# Mistake 4

## Confusing ClusterIP with Pod IP

ClusterIP:

Stable virtual IP.

Pod IP:

Temporary.

Applications should use the Service, not the Pod.

---

# Mistake 5

## Using NodePort in Production

NodePort works well for:

* Development
* Home labs
* Testing

Production environments should usually use:

* LoadBalancer
* Ingress

---

# Mistake 6

## Assuming LoadBalancer Works Everywhere

Cloud providers automatically provision load balancers.

Local clusters usually do not.

Examples:

* Minikube
* Kind
* Docker Desktop

Use MetalLB or a cloud provider for local LoadBalancer functionality.

---

# Mistake 7

## Forgetting the Ingress Controller

Creating an Ingress resource alone does nothing.

An Ingress Controller must be installed and running.

Verify:

```bash
kubectl get pods -n ingress-nginx
```

---

# Mistake 8

## Ignoring Endpoints

Many engineers debug the Service first.

Instead, check:

```bash
kubectl get endpoints
```

If Endpoints are empty:

The Service cannot forward traffic.

---

# Mistake 9

## Assuming DNS Is Broken

Many "DNS problems" are actually:

* Missing Service
* Empty Endpoints
* Selector mismatch

Verify:

```bash
nslookup <service>

kubectl get svc

kubectl get endpoints
```

---

# Mistake 10

## Forgetting Namespaces

The Service exists:

```text
payments
```

Application runs in:

```text
default
```

Using:

```text
payments
```

fails.

Use:

```text
payments.default.svc.cluster.local
```

or create the Service in the same namespace.

---

# Mistake 11

## Ignoring NetworkPolicies

Pods appear healthy.

Services look correct.

DNS works.

Traffic still fails.

Reason:

NetworkPolicy blocks traffic.

Verify:

```bash
kubectl get networkpolicy

kubectl describe networkpolicy
```

---

# Mistake 12

## Assuming ExternalName Creates a Proxy

ExternalName only returns a DNS alias.

It does not:

* Load balance
* Proxy traffic
* Create Endpoints

---

# Mistake 13

## Forgetting That EndpointSlices Replace Endpoints

Many engineers still troubleshoot only:

```bash
kubectl get endpoints
```

Modern Kubernetes also uses:

```bash
kubectl get endpointslice
```

---

# Mistake 14

## Ignoring kube-proxy

Services depend on kube-proxy to route traffic.

If kube-proxy is unhealthy:

Service networking may fail.

Verify:

```bash
kubectl get pods -n kube-system
```

---

# Mistake 15

## Assuming Ingress Works for All Protocols

Ingress is primarily designed for:

* HTTP
* HTTPS

For TCP or UDP applications, additional configuration or different networking solutions may be required.

---

# Quick Comparison

| Incorrect Assumption         | Correct Understanding                      |
| ---------------------------- | ------------------------------------------ |
| Pod IPs are permanent        | Pod IPs change                             |
| ClusterIP equals Pod IP      | ClusterIP is a virtual Service IP          |
| Services route to all Pods   | Only Ready Pods receive traffic            |
| Ingress works by itself      | Requires an Ingress Controller             |
| ExternalName creates a proxy | It creates a DNS alias only                |
| Endpoints always exist       | Selector mismatches create empty Endpoints |

---

# Production Checklist

Before modifying networking resources, verify:

* Pods are Running and Ready
* Service selectors
* Pod labels
* Endpoints
* EndpointSlices
* DNS resolution
* CoreDNS health
* Ingress Controller
* NetworkPolicies
* kube-proxy

---

# Memory Tips

✔ ClusterIP → Internal communication

✔ NodePort → Node IP + Port

✔ LoadBalancer → Cloud external access

✔ ExternalName → DNS alias

✔ Endpoints → Backend Pods

✔ EndpointSlice → Scalable Endpoints

✔ CoreDNS → DNS resolution

✔ Ingress → HTTP/HTTPS routing

✔ NetworkPolicy → Pod firewall

---

# Final Advice

Most Kubernetes networking issues are not caused by Kubernetes itself.

They are usually caused by:

* Incorrect labels
* Selector mismatches
* Missing Endpoints
* Missing Ingress Controller
* Namespace confusion
* NetworkPolicies
* DNS assumptions

Always troubleshoot methodically:

1. Pods
2. Services
3. Endpoints
4. DNS
5. Ingress
6. NetworkPolicies
7. kube-proxy
