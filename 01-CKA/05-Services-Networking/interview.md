# Services & Networking - Interview Questions

> This document contains Kubernetes Services and Networking interview questions ranging from beginner to senior DevOps engineer level.

---

# 🟢 Beginner Level

---

# 1. What is a Kubernetes Service?

## Short Answer

A Service is a Kubernetes resource that provides a stable network identity and load balances traffic to one or more Pods.

### Why do we need Services?

Pods are ephemeral:

* Pod IPs change
* Pods are recreated
* Pods are rescheduled

Services provide a stable IP and DNS name.

### Production Insight

Applications should communicate using Service names instead of Pod IPs.

---

# 2. Why can't applications communicate directly using Pod IPs?

## Short Answer

Pod IPs are temporary.

Whenever a Pod is recreated, it receives a new IP address.

Using Pod IPs directly would cause application failures.

---

# 3. What are the different Service types?

| Type         | Purpose                       |
| ------------ | ----------------------------- |
| ClusterIP    | Internal communication        |
| NodePort     | External access via node port |
| LoadBalancer | Cloud load balancer           |
| ExternalName | Maps to an external DNS name  |

---

# 4. What is ClusterIP?

## Short Answer

ClusterIP is the default Service type.

It exposes an application only inside the Kubernetes cluster.

### Typical Use Cases

* Backend APIs
* Databases
* Internal microservices

---

# 5. What is NodePort?

## Short Answer

NodePort exposes a Service on a static port on every node.

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

### Typical Use Cases

* Labs
* Testing
* Development environments

---

# 🟡 Intermediate Level

---

# 6. What is LoadBalancer?

## Short Answer

LoadBalancer creates an external load balancer through the cloud provider and forwards traffic to a Service.

### Typical Use Cases

* Public APIs
* Production web applications

---

# 7. What is ExternalName?

## Short Answer

ExternalName maps a Kubernetes Service to an external DNS name.

It does not create a proxy or load balance traffic.

### Example

```yaml
type: ExternalName
externalName: api.example.com
```

---

# 8. What are Endpoints?

## Short Answer

Endpoints represent the Pods backing a Service.

Whenever Pods are added or removed, Kubernetes automatically updates the Endpoints object.

---

# 9. What is EndpointSlice?

## Short Answer

EndpointSlice is the scalable replacement for Endpoints.

### Advantages

* Better scalability
* Reduced API server load
* Improved performance
* Efficient updates for large clusters

---

# 10. What is CoreDNS?

## Short Answer

CoreDNS is the DNS server used by Kubernetes.

It resolves:

* Service names
* Pod names (where applicable)
* External DNS queries

Without CoreDNS, Service discovery by name does not work.

---

# 🔴 Senior Level

---

# 11. Explain the traffic flow from an external user to a Pod.

## Answer

```text
Client
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
```

Each component has a different responsibility:

* LoadBalancer exposes the application externally.
* Ingress performs HTTP/HTTPS routing.
* Service provides stable networking.
* Endpoint/EndpointSlice identifies backend Pods.

---

# 12. What is kube-proxy?

## Short Answer

kube-proxy maintains network rules on each node so that Services can route traffic to backend Pods.

### Responsibilities

* Service load balancing
* Network rule management
* Forwarding traffic to healthy Pods

---

# 13. What is the difference between Endpoints and EndpointSlice?

| Feature     | Endpoints | EndpointSlice |
| ----------- | --------- | ------------- |
| Legacy      | Yes       | No            |
| Scalability | Limited   | Excellent     |
| Performance | Lower     | Higher        |
| Recommended | No        | Yes           |

---

# 14. What is Ingress?

## Short Answer

Ingress provides HTTP and HTTPS routing into a Kubernetes cluster.

### Features

* Host-based routing
* Path-based routing
* TLS termination
* Multiple Services behind one IP

---

# 15. What is a NetworkPolicy?

## Short Answer

A NetworkPolicy controls which Pods are allowed to communicate.

Without NetworkPolicies:

All Pods can communicate.

With NetworkPolicies:

Only explicitly allowed traffic is permitted.

---

# 16. Explain ClusterIP vs NodePort vs LoadBalancer.

| Feature        | ClusterIP | NodePort | LoadBalancer |
| -------------- | --------- | -------- | ------------ |
| Internal       | ✅         | ✅        | ✅            |
| External       | ❌         | ✅        | ✅            |
| Cloud Required | ❌         | ❌        | ✅            |
| Production     | ✅         | Limited  | ✅            |

---

# 17. When should you use Ingress instead of LoadBalancer?

## Answer

Use Ingress when:

* Multiple applications share one external IP.
* HTTP/HTTPS routing is required.
* TLS termination is needed.
* Host or path-based routing is required.

---

# 🏢 Production Scenarios

---

# 18. A Service has no Endpoints. What would you check?

### Investigation

```bash
kubectl get svc

kubectl describe svc <service>

kubectl get endpoints

kubectl get pods --show-labels
```

### Common Causes

* Selector mismatch
* Pods not Ready
* Pods not running

---

# 19. DNS resolution fails inside a Pod. How would you troubleshoot?

### Investigation

```bash
kubectl exec -it <pod> -- sh

nslookup kubernetes.default

kubectl get pods -n kube-system

kubectl logs -n kube-system deployment/coredns
```

### Possible Causes

* CoreDNS unavailable
* Incorrect DNS configuration
* NetworkPolicy blocking DNS traffic

---

# 20. Ingress returns HTTP 404. What would you check?

### Investigation

```bash
kubectl get ingress

kubectl describe ingress

kubectl get svc

kubectl get endpoints
```

### Possible Causes

* Incorrect host rule
* Incorrect path
* Missing backend Service
* Ingress Controller not running

---

# 21. Pods cannot communicate. How would you troubleshoot?

### Investigation

1. Verify Pods are running.
2. Verify Services.
3. Verify Endpoints.
4. Verify DNS.
5. Verify NetworkPolicies.
6. Test connectivity from inside the cluster.

---

# 22. Why would a Service have no Endpoints even though Pods exist?

### Possible Causes

* Selector labels do not match.
* Pods are not Ready.
* Pods are in another namespace.
* Incorrect labels were applied.

---

# Common Interview Follow-up Questions

* How does kube-proxy work?
* Why are Services needed?
* Why not use Pod IPs?
* What is the difference between Endpoints and EndpointSlice?
* How does DNS work in Kubernetes?
* Why use Ingress?
* When should NodePort be avoided?
* Can multiple Services target the same Pods?
* What happens if CoreDNS fails?
* Does NetworkPolicy deny traffic by default?

---

# Interviewer's Perspective

Senior interviewers usually move beyond definitions.

Instead of asking:

> "What is a Service?"

They ask questions like:

* Walk me through how a request reaches a Pod.
* Why are there no Endpoints for this Service?
* Why is DNS failing inside one namespace but not another?
* Why is the Ingress returning 404?
* How would you expose multiple applications using one IP address?
* How would you secure Pod-to-Pod communication?

Strong candidates explain the complete traffic flow, identify likely failure points, and describe a structured troubleshooting process rather than jumping directly to configuration changes.

---

# Real Interview Scenario

### Scenario

You have:

* A Deployment with three Pods.
* A ClusterIP Service.
* An Ingress resource.
* An Ingress Controller.

Users report that the application is unavailable.

### Explain your troubleshooting workflow.

A strong answer should include:

1. Verify Pods are Running and Ready.
2. Verify the Service selector matches the Pod labels.
3. Verify Endpoints or EndpointSlices exist.
4. Test Service connectivity from inside the cluster.
5. Verify DNS resolution.
6. Check the Ingress resource configuration.
7. Verify the Ingress Controller is healthy.
8. Review Events and logs.
9. Identify the root cause.
10. Apply the minimum required fix and verify application recovery.
