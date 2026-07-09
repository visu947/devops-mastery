
# CKA YAML Skeleton Cheat Sheet

> Goal: **Remember the skeleton, not the entire YAML.** Fill only the
> values required by the question.

# Kubernetes YAML Fundamentals

Before learning any Kubernetes resource, remember that **most Kubernetes objects follow the same structure**.

```yaml
apiVersion:
kind:
metadata:
spec:
```

Think of every Kubernetes YAML as answering four questions:

| Field          | Meaning                                                                                                                                 | Memory Trick    |
| -------------- | ----------------------------------------------------------------------------------------------------------------------------------------| --------------- |
| **apiVersion** | Which Kubernetes API understands this resource?                                                                                         | Which API?      |
| **kind**       | What Kubernetes object are you creating?                                                                                                | What object?    |
| **metadata**   | Information that identifies the object (name, namespac, labels, annotations, ownerReferences, finalizers)                               |  Who am I?      |
| **spec**       | The desired state that Kubernetes should create and maintain (containers, volumes, restartPolicy, nodeSelector, affinity, tolerations)  | What do I want? |

```text
metadata

├── name            → Your name
├── namespace       → Your city
├── labels          → Categories/tags
├── annotations     → Extra notes
├── ownerReferences → Who owns you
└── finalizers      → Cleanup before deletion
```
```text
## spec – Desired State (What should Kubernetes build?)

```text
spec

├── replicas
│      → How many Pods?
│      → Resource: Deployment, ReplicaSet, StatefulSet
│
├── selector
│      → Which objects?
│      → Resource: Deployment, ReplicaSet, Service, NetworkPolicy
│
├── template
│      → What should be created?
│      → Resource: Deployment, ReplicaSet, DaemonSet, StatefulSet, Job, CronJob
│
├── containers
│      → What runs?
│      → Resource: Pod
│
├── image
│      → Which application?
│      → Resource: Pod
│
├── command
│      → Override container ENTRYPOINT
│      → Resource: Pod
│
├── args
│      → Override container CMD
│      → Resource: Pod
│
├── env
│      → Environment variables
│      → Resource: Pod
│
├── ports
│      → Network ports
│      → Resource: Pod, Service
│
├── resources
│      → CPU & Memory requests/limits
│      → Resource: Pod
│
├── volumes
│      → Storage definitions
│      → Resource: Pod
│
├── volumeMounts
│      → Mount storage into containers
│      → Resource: Pod
│
├── accessModes
│      → Storage access (RWO, ROX, RWX)
│      → Resource: PersistentVolume, PersistentVolumeClaim
│
├── storageClassName
│      → Which storage backend?
│      → Resource: PersistentVolumeClaim, PersistentVolume
│
├── type
│      → Service type (ClusterIP, NodePort, LoadBalancer)
│      → Resource: Service
│
├── rules
│      → HTTP routing rules
│      → Resource: Ingress
│
├── policyTypes
│      → Ingress/Egress policy
│      → Resource: NetworkPolicy
│
├── ingress
│      → Incoming traffic rules
│      → Resource: NetworkPolicy, Ingress
│
├── egress
│      → Outgoing traffic rules
│      → Resource: NetworkPolicy
│
├── restartPolicy
│      → Pod restart behavior
│      → Resource: Pod, Job, CronJob
│
├── serviceName
│      → Headless Service used by StatefulSet
│      → Resource: StatefulSet
│
├── schedule
│      → Cron expression
│      → Resource: CronJob
│
├── jobTemplate
│      → Template for Jobs created on schedule
│      → Resource: CronJob
│
└── tls
       → HTTPS certificates
       → Resource: Ingress
```

### Memory Trick

```text
spec

├── Scaling
│      replicas
│
├── Selection
│      selector
│
├── Pod Configuration
│      template
│      containers
│      image
│      command
│      args
│      env
│      ports
│      resources
│      volumes
│      volumeMounts
│      restartPolicy
│
├── Storage
│      accessModes
│      storageClassName
│
├── Networking
│      type
│      rules
│      ingress
│      egress
│      policyTypes
│      tls
│
└── Scheduling
       schedule
       jobTemplate
       serviceName
```

### CKA Golden Rule

```text
Deployment
    ↓
replicas + selector + template

Pod
    ↓
containers + image + command + env + volumes

Service
    ↓
selector + type + ports

Ingress
    ↓
rules + backend + tls

PVC
    ↓
accessModes + storage + storageClassName

NetworkPolicy
    ↓
podSelector + ingress + egress

StatefulSet
    ↓
replicas + serviceName + volumeClaimTemplates

CronJob
    ↓
schedule + jobTemplate
```

```
                   

---

## Mental Model

```text
Kubernetes Object

↓

Which API?

↓

What Object?

↓

Who am I?

↓

What do I want Kubernetes to build?
```

or simply:

```text
API
↓

Object
↓

Identity
↓

Desired State
```

---

## Examples

### Pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx

spec:
  containers:
```

### Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx

spec:
  replicas:
```

### Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service

spec:
  selector:
  ports:
```

### PersistentVolumeClaim (PVC)

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc

spec:
  accessModes:
```

### Ingress

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress

spec:
  rules:
```

Notice that the first three fields stay the same. **Only the contents of `spec` change depending on the resource.**

---

# Resources That Do NOT Use `spec`

Some Kubernetes objects simply store configuration or data.

## ConfigMap

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config

data:
  color: blue
```

## Secret

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-secret

type: Opaque

data:
  password: <base64-value>
```

These resources use **`data`** instead of **`spec`** because Kubernetes is storing information rather than managing a running workload.

---

# CKA Memory Rule

If the resource **runs or manages something**, it almost always follows:

```yaml
apiVersion:
kind:
metadata:
spec:
```

Examples:

* Pod
* Deployment
* Service
* DaemonSet
* StatefulSet
* Job
* CronJob
* Ingress
* NetworkPolicy
* PersistentVolume
* PersistentVolumeClaim
* HorizontalPodAutoscaler

If the resource **stores configuration or data**, it usually follows:

```yaml
apiVersion:
kind:
metadata:
data:
```

or

```yaml
apiVersion:
kind:
metadata:
type:
data:
```

Examples:

* ConfigMap
* Secret

---

# Interview Tip

**apiVersion**

> Which Kubernetes API group/version manages this resource.

**kind**

> The type of Kubernetes object to create.

**metadata**

> Identity of the object (name, namespace, labels, annotations).

**spec**

> The desired state Kubernetes should maintain.

---

# CKA Gold Tip

Don't memorize dozens of YAML files.

Memorize this:

```text
apiVersion
↓

kind
↓

metadata
↓

spec
```

Then learn only **what goes inside `spec`** for each resource. That is how experienced Kubernetes engineers think and how you should approach the CKA exam.



------------------------------------------------------------------------

# 1. Pod

``` yaml
apiVersion: v1
kind: Pod
metadata:
  name:

spec:
  containers:
  - name:
    image:
```

Common additions:

``` yaml
command:
args:
env:
ports:
resources:
volumeMounts:
```

------------------------------------------------------------------------

# 2. Deployment

``` yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name:

spec:
  replicas:

  selector:
    matchLabels:
      app:

  template:
    metadata:
      labels:
        app:

    spec:
      containers:
      - name:
        image:
```

Common additions:

``` yaml
command:
env:
resources:
volumeMounts:
```

------------------------------------------------------------------------

# 3. Service

``` yaml
apiVersion: v1
kind: Service
metadata:
  name:

spec:
  selector:
    app:

  ports:
  - port:
    targetPort:
```

NodePort:

``` yaml
spec:
  type: NodePort

  ports:
  - port:
    targetPort:
    nodePort:
```

------------------------------------------------------------------------

# 4. ConfigMap

``` yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name:

data:
  key: value
```

------------------------------------------------------------------------

# 5. Secret

``` yaml
apiVersion: v1
kind: Secret
metadata:
  name:

type: Opaque

data:
  key:
```

------------------------------------------------------------------------

# 6. PersistentVolumeClaim (PVC)

``` yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name:

spec:
  accessModes:
  - ReadWriteOnce

  resources:
    requests:
      storage:

  storageClassName:
```

------------------------------------------------------------------------

# 7. PersistentVolume (PV)

``` yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name:

spec:
  capacity:
    storage:

  accessModes:
  - ReadWriteOnce

  storageClassName:

  hostPath:
    path:
```

------------------------------------------------------------------------

# 8. StorageClass

``` yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name:

provisioner:

parameters:
```

------------------------------------------------------------------------

# 9. Ingress

``` yaml
apiVersion: networking.k8s.io/v1
kind: Ingress

metadata:
  name:

spec:
  rules:
  - http:
      paths:
      - path:
        pathType: Prefix

        backend:
          service:
            name:
            port:
              number:
```

SSL Redirect:

``` yaml
metadata:
  annotations:
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
```

------------------------------------------------------------------------

# 10. NetworkPolicy

``` yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy

metadata:
  name:

spec:
  podSelector:

  policyTypes:
  - Ingress

  ingress:
```

------------------------------------------------------------------------

# 11. Namespace

``` yaml
apiVersion: v1
kind: Namespace

metadata:
  name:
```

------------------------------------------------------------------------

# 12. ServiceAccount

``` yaml
apiVersion: v1
kind: ServiceAccount

metadata:
  name:
```

------------------------------------------------------------------------

# 13. Role

``` yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role

metadata:
  name:

rules:
- apiGroups:
  resources:
  verbs:
```

------------------------------------------------------------------------

# 14. RoleBinding

``` yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding

metadata:
  name:

subjects:

roleRef:
```

------------------------------------------------------------------------

# 15. CronJob

``` yaml
apiVersion: batch/v1
kind: CronJob

metadata:
  name:

spec:
  schedule:

  jobTemplate:
    spec:
      template:
        spec:
          containers:
          restartPolicy:
```

------------------------------------------------------------------------

# 16. Job

``` yaml
apiVersion: batch/v1
kind: Job

metadata:
  name:

spec:
  template:
    spec:
      containers:
      restartPolicy:
```

------------------------------------------------------------------------

# 17. DaemonSet

``` yaml
apiVersion: apps/v1
kind: DaemonSet

metadata:
  name:

spec:
  selector:

  template:
    metadata:

    spec:
      containers:
```

------------------------------------------------------------------------

# 18. StatefulSet

``` yaml
apiVersion: apps/v1
kind: StatefulSet

metadata:
  name:

spec:
  serviceName:

  replicas:

  selector:

  template:

  volumeClaimTemplates:
```

------------------------------------------------------------------------

# 19. HorizontalPodAutoscaler (HPA)

``` yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler

metadata:
  name:

spec:
  scaleTargetRef:

  minReplicas:

  maxReplicas:

  metrics:
```

------------------------------------------------------------------------

# CKA Memory Rule

    Can kubectl generate it?

    YES
    ↓
    Generate → Edit → Apply

    NO
    ↓
    Use Skeleton
    ↓
    Fill values
    ↓
    Apply

# High-Priority Skeletons

1.  Pod
2.  Deployment
3.  Service
4.  PVC
5.  Ingress
6.  NetworkPolicy





# Services & Networking - Cheat Sheet

> **One-page quick revision guide for Kubernetes Services, Networking, DNS, and Ingress**

---

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
