# Services & Networking - Cheat Sheet

> **One-page quick revision guide for Kubernetes Services, Networking, DNS, and Ingress**

---

```text

                    Internet
                        │
                        ▼
               AWS Load Balancer
                        │
                        ▼
             Ingress Controller
                        │
      ┌─────────────────┼─────────────────┐
      ▼                 ▼                 ▼
 payment-service   order-service   inventory-service
      │                 │                 │
   ┌──┴──┐           ┌──┴──┐          ┌───┴───┐
   ▼     ▼           ▼     ▼          ▼       ▼
Pod1   Pod2        Pod1   Pod2      Pod1    Pod2

```

# Kubernetes Networking Flow

```text
External User
      │
      ▼
LoadBalancer
      │
      ▼
Ingress
      │
      ▼
Service
      │
      ▼
Endpoint / EndpointSlice
      │
      ▼
Pod
      │
      ▼
Container
```

Internal communication:

```text
Application
      │
      ▼
ClusterIP Service
      │
      ▼
Endpoint
      │
      ▼
Pod
```

---

# Service Decision Tree

```text
Need internal communication?
        │
        ▼
ClusterIP

Need quick external testing?
        │
        ▼
NodePort

Need production external access?
        │
        ▼
LoadBalancer

Need HTTP/HTTPS routing?
        │
        ▼
Ingress

Need external DNS mapping?
        │
        ▼
ExternalName
```

---

# Service Types

| Service Type | Internal | External | Typical Use Case            |
| ------------ | -------- | -------- | --------------------------- |
| ClusterIP    | ✅        | ❌        | Internal APIs, databases    |
| NodePort     | ✅        | ✅        | Labs, testing               |
| LoadBalancer | ✅        | ✅        | Production applications     |
| ExternalName | ❌        | DNS only | External databases and APIs |

---

# ClusterIP

### Purpose

Internal communication inside the cluster.

Traffic Flow

```text
Pod
   │
   ▼
ClusterIP
   │
   ▼
Pod
```

Default Service type.

---

# NodePort

### Purpose

Expose an application through every worker node.

Traffic Flow

```text
Client
   │
   ▼
NodeIP:NodePort
   │
   ▼
Service
   │
   ▼
Pod
```

---

# LoadBalancer

### Purpose

Expose applications through a cloud provider load balancer.

Traffic Flow

```text
Internet
     │
     ▼
LoadBalancer
     │
     ▼
Service
     │
     ▼
Pod
```

---

# ExternalName

### Purpose

Map a Kubernetes Service to an external DNS name.

Example

```yaml
type: ExternalName
externalName: api.example.com
```

No proxy is created.

---

# Endpoints

Endpoints represent the Pods backing a Service.

```text
Service
    │
    ▼
Endpoints
    │
 ┌──┴──┐
 ▼     ▼
Pod1  Pod2
```

If Endpoints are empty:

✔ Check Service selectors.

✔ Check Pod labels.

---

# EndpointSlice

Modern replacement for Endpoints.

Benefits:

* Better scalability
* Better performance
* Lower API server load
* Efficient updates for large clusters

---

# DNS

Every Service automatically receives a DNS name.

Example:

```text
backend.default.svc.cluster.local
```

Most applications simply use:

```text
backend
```

CoreDNS resolves the name.

---

# CoreDNS

Responsibilities:

* Service discovery
* Pod name resolution
* DNS caching
* External DNS forwarding

Without CoreDNS:

❌ Service names cannot be resolved.

---

# Ingress

### Purpose

HTTP and HTTPS routing.

Supports:

* Host-based routing
* Path-based routing
* TLS termination
* Multiple applications behind one IP

Traffic Flow

```text
Internet
     │
     ▼
Ingress
     │
     ▼
Service
     │
     ▼
Pod
```

---

# NetworkPolicy

### Purpose

Control Pod-to-Pod communication.

Without NetworkPolicy

```text
All Pods communicate.
```

With NetworkPolicy

```text
Only approved traffic.
```

---

# Comparison Table

| Feature          | ClusterIP | NodePort   | LoadBalancer | Ingress |
| ---------------- | --------- | ---------- | ------------ | ------- |
| Internal Access  | ✅         | ✅          | ✅            | ❌       |
| External Access  | ❌         | ✅          | ✅            | ✅       |
| HTTP Routing     | ❌         | ❌          | ❌            | ✅       |
| TLS Termination  | ❌         | ❌          | ❌            | ✅       |
| Production Ready | ✅         | ⚠️ Limited | ✅            | ✅       |

---

# Endpoints vs EndpointSlice

| Feature        | Endpoints | EndpointSlice |
| -------------- | --------- | ------------- |
| Legacy         | ✅         | ❌             |
| Recommended    | ❌         | ✅             |
| Scalability    | Limited   | Excellent     |
| API Efficiency | Lower     | Higher        |

---

# Common Troubleshooting Workflow

```text
Application Fails
       │
       ▼
Pods Running?
       │
       ▼
Service Exists?
       │
       ▼
Endpoints Created?
       │
       ▼
DNS Working?
       │
       ▼
Ingress Correct?
       │
       ▼
NetworkPolicy Blocking?
```

---

# Common Commands

```bash
kubectl get pods -o wide

kubectl get svc

kubectl get endpoints

kubectl get endpointslice

kubectl describe svc <service>

kubectl get ingress

kubectl get networkpolicy

kubectl exec -it <pod> -- sh

nslookup <service>

wget -qO- http://<service>
```

---

# Memory Tricks

## ClusterIP

Internal only.

---

## NodePort

Node IP + Port.

---

## LoadBalancer

Cloud load balancer.

---

## ExternalName

External DNS alias.

---

## Endpoints

Service → Pods.

---

## EndpointSlice

Scalable Endpoints.

---

## CoreDNS

Service name resolution.

---

## Ingress

HTTP/HTTPS routing.

---

## NetworkPolicy

Firewall for Pods.

---

# CKA Exam Tips

✔ Empty Endpoints usually mean a selector mismatch.

✔ Service DNS not working?

Check CoreDNS.

✔ Ingress without an Ingress Controller will not route traffic.

✔ Always verify Pods before troubleshooting Services.

✔ NetworkPolicy can silently block traffic.

✔ Use DNS names instead of Pod IP addresses.

---

# Production Examples

| Requirement                 | Kubernetes Feature |
| --------------------------- | ------------------ |
| Internal API                | ClusterIP          |
| Quick testing               | NodePort           |
| Internet access             | LoadBalancer       |
| Multiple websites on one IP | Ingress            |
| External SaaS API           | ExternalName       |
| Service discovery           | DNS + CoreDNS      |
| Secure Pod communication    | NetworkPolicy      |

---

# Quick Review Checklist

☐ ClusterIP

☐ NodePort

☐ LoadBalancer

☐ ExternalName

☐ Endpoints

☐ EndpointSlice

☐ DNS

☐ CoreDNS

☐ Ingress

☐ NetworkPolicy

☐ Service Troubleshooting

☐ Traffic Flow
