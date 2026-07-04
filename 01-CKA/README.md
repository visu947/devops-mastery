``` text
I'll summarize everything we've completed

# Module 1 - Kubernetes Architecture ✅

We covered:

✅ Control Plane
├── Concept: Brain of Kubernetes containing management components.
├── Production: Usually 3 Control Plane nodes for High Availability.
├── Best Practice: Never run a single control plane in production.
├── Interview Tip: Worker nodes can fail; control plane should remain available.
└── Questions I Asked
    Q. What is the most important component?
    A. etcd. Without etcd Kubernetes loses its desired state.

---------------------------------------------------------

✅ API Server
├── Concept: Entry point for every Kubernetes request.
├── Production: All components communicate through the API Server.
├── Best Practice: Never access etcd directly from applications.
├── Interview Tip: API Server is stateless; etcd stores the data.
└── Questions I Asked
    Q. Does Scheduler talk directly to etcd?
    A. No. Scheduler communicates only with the API Server.

---------------------------------------------------------

✅ Scheduler
├── Concept: Chooses the best worker node for Pods.
├── Production: Uses Requests, Affinity, Taints, Resources and Policies.
├── Best Practice: Always define CPU/Memory Requests.
├── Interview Tip: Scheduler never starts Pods; kubelet does.
└── Questions I Asked
    Q. Does Scheduler use Limits?
    A. No. Scheduling decisions are based on Requests.

---------------------------------------------------------

✅ Controller Manager
├── Concept: Continuously reconciles actual state to desired state.
├── Production: Runs Deployment, ReplicaSet, Node and Job controllers.
├── Best Practice: Never manually recreate Pods managed by Deployments.
├── Interview Tip: Controllers continuously watch the API Server.

---------------------------------------------------------

✅ etcd
├── Concept: Distributed key-value database storing Kubernetes desired state.
├── Production: Run 3 or 5 member HA clusters.
├── Best Practice: Take regular snapshots.
├── Interview Tip: etcd stores metadata, not application data.
└── Questions I Asked
    Q. What should be backed up?
    A. etcd + Persistent Volumes.

    Q. Does Velero backup etcd?
    A. No. Velero backs up Kubernetes objects via the API Server.

---------------------------------------------------------

✅ Worker Nodes
├── Concept: Execute application workloads.
├── Production: Scale horizontally using Cluster Autoscaler.
├── Best Practice: Keep workers stateless.
├── Interview Tip: Losing a worker shouldn't affect the cluster permanently.

---------------------------------------------------------

✅ kubelet
├── Concept: Kubernetes agent running on every worker node.
├── Production: Communicates with the API Server.
├── Best Practice: kubelet should never be stopped manually.
├── Interview Tip: kubelet creates Pods through the container runtime.

---------------------------------------------------------

✅ kube-proxy
├── Concept: Implements Kubernetes Services networking.
├── Production: Maintains iptables/IPVS rules.
├── Best Practice: Usually managed automatically.
├── Interview Tip: kube-proxy is not the CNI plugin.

---------------------------------------------------------

✅ Container Runtime
├── Concept: Runs containers (containerd, CRI-O, etc.).
├── Production: containerd is most commonly used today.
├── Best Practice: Docker Engine is no longer required.
├── Interview Tip: kubelet communicates with runtime through CRI.

Understanding: 100%

Module 2 - Workloads ✅

We covered:

✅ Pod
✅ ReplicaSet
✅ Deployment
✅ DaemonSet
✅ StatefulSet
✅ Jobs
✅ CronJobs

Understanding: 100%

Module 3 - Scheduling ✅

We covered:

✅ Labels
✅ Selectors
✅ NodeSelector
✅ Node Affinity
✅ Pod Affinity
✅ Pod Anti-Affinity
✅ Taints
✅ Tolerations
✅ PriorityClass

Understanding: 95%

Module 4 - Networking ✅

We covered in great detail:

✅ CNI
✅ Calico
✅ Cilium
✅ Flannel
✅ Pod Networking
✅ ClusterIP
✅ NodePort
✅ LoadBalancer
✅ Ingress
✅ Ingress Controller
✅ CoreDNS
✅ Endpoints
✅ EndpointSlices
✅ NetworkPolicies

Understanding: 98%

Module 5 - Storage ✅

This became one of our deepest discussions.

We covered:

✅ Volumes
✅ emptyDir
✅ hostPath
✅ ConfigMap Volumes
✅ Secret Volumes
✅ PersistentVolume
✅ PersistentVolumeClaim
✅ StorageClass
✅ CSI Driver
✅ Static Provisioning
✅ Dynamic Provisioning
✅ StatefulSets
✅ Database Storage
✅ Access Modes
✅ Reclaim Policies
✅ Volume Expansion
✅ Volume Binding Mode
✅ Snapshots
✅ VolumeSnapshot
✅ VolumeSnapshotContent
✅ VolumeSnapshotClass
✅ Restore
✅ CSI Internals

Understanding: 98%

Module 6 - Security ✅

We covered:

Identity
✅ Users
✅ Groups
✅ ServiceAccounts
Authentication
✅ OIDC
✅ ServiceAccount Tokens
✅ Certificates
Authorization
✅ RBAC
✅ Role
✅ ClusterRole
✅ RoleBinding
✅ ClusterRoleBinding
Admission
✅ Admission Controllers
✅ Mutating
✅ Validating
✅ Pod Security Admission
Runtime Security
✅ SecurityContext
✅ Pod Security
✅ Privileged Containers
✅ Linux Capabilities
✅ runAsNonRoot
✅ readOnlyRootFilesystem
Secret Management
✅ Kubernetes Secrets
✅ Vault
✅ External Secrets Operator
✅ Vault Agent
✅ Secrets Store CSI Driver
✅ Secret Rotation
Image Security
✅ Image Registry Policies
✅ Image Scanning
✅ Image Signing
✅ Immutable Tags
Network Security
✅ NetworkPolicies
✅ Zero Trust
Audit Logging
✅ Kubernetes Audit Logs

Understanding: 95%

GitOps / Platform Topics ✅

Because of your background, we also covered:

✅ FluxCD
✅ Helm
✅ GitOps
✅ Operators
✅ CSI Drivers
✅ External Secrets Operator
✅ Ingress Controller

Understanding: 95%

Real Production Topics ✅

We discussed:

AWS Load Balancers
EBS CSI Driver
Vault
PostgreSQL
Secret Rotation
Stateful Databases
Disaster Recovery
Snapshots
Dynamic Credentials

These are topics beyond a typical CKA course.

Where are you now?

If I had to rate your understanding:

Area	Understanding
Architecture	100%
Workloads	100%
Scheduling	95%
Networking	98%
Storage	98%
Security	95%
GitOps   95%

Kubernetes (Remaining)

Kubernetes

├─────────────────────────────────────────────
│ 1. Resource Management ⭐⭐⭐⭐⭐
├─────────────────────────────────────────────

    CPU
    ├── CPU Units (Core, millicores)
    ├── CPU Scheduling
    ├── CPU Requests
    ├── CPU Limits
    ├── CPU Throttling
    └── CPU Troubleshooting

    Memory
    ├── Memory Units
    ├── Memory Requests
    ├── Memory Limits
    ├── OOMKilled
    ├── Evictions
    └── Memory Troubleshooting

    Scheduler
    ├── Scheduler Decisions
    ├── Requests vs Limits
    ├── Allocatable Resources
    ├── Pending Pods
    └── Scheduling Failures
    ├── QoS Classes

        ┌─────────────────────────────┐
        │ 🟢 Guaranteed               │
        │ Requests = Limits           │
        │ Highest protection          │
        │ Last to be evicted          │
        └─────────────────────────────┘
                    ▲
                    │
        ┌─────────────────────────────┐
        │ 🟡 Burstable                │
        │ Requests < Limits           │
        │ Can burst when resources    │
        │ Medium eviction priority    │
        └─────────────────────────────┘
                    ▲
                    │
        ┌─────────────────────────────┐
        │ 🔴 BestEffort               │
        │ No Requests                 │
        │ No Limits                   │
        │ First to be evicted         │
        └─────────────────────────────┘

    ├── Guaranteed
    ├── Burstable
    ├── BestEffort
    ├── Eviction Priority
    └── Production Examples

    ResourceQuota
    ├── Namespace Limits
    ├── Object Count Quotas
    ├── Compute Quotas
    └── Production Examples

    LimitRange
    ├── Default Requests
    ├── Default Limits
    ├── Min Resources
    ├── Max Resources
    └── Production Examples

    Production Troubleshooting
    ├── Pending
    ├── OOMKilled
    ├── CrashLoopBackOff
    ├── Evicted
    ├── Insufficient CPU
    ├── Insufficient Memory
    └── kubectl Commands

─────────────────────────────────────────────
│ 2. Observability ⭐⭐⭐⭐⭐
─────────────────────────────────────────────

 Observability & Troubleshooting
├── Logs
│   ├── kubectl logs
│   ├── Previous logs
│   ├── Multi-container Pods
│   └── Streaming logs
│
├── Describe
│   ├── kubectl describe
│   ├── Events
│   └── Object inspection
│
├── Exec & Debug
│   ├── kubectl exec
│   ├── kubectl cp
│   ├── kubectl debug
│   └── Ephemeral containers
│
├── Metrics
│   ├── kubectl top
│   ├── Metrics Server
│   └── Resource usage
│
├── Probes
│   ├── Liveness
│   ├── Readiness
│   └── Startup
|    | Probe     | Failure Result                                                                                          |
|    | --------- | ------------------------------------------------------------------------------------------------------- |
|    | Startup   | Wait for startup (if it never succeeds within the configured threshold, kubelet restarts the container) |
|    | Liveness  | Restart the container                                                                                   |
|    | Readiness | Remove the Pod from Service endpoints (no restart)                                                      |
|    ----------------------------------------------------------------------------------------------------------------------|
│
├── Production Monitoring
│   ├── Prometheus
             Prometheus

     ┌──────────┼──────────────┐

     ▼          ▼              ▼

 kubelet    Application     Node Exporter

(cAdvisor)   /metrics          OS Metrics

     │          │                 │

CPU        HTTP Requests      Disk

Memory     JVM GC            Filesystem

Network    Cache             Load Average


│   ├── Grafana
│   ├── Alertmanager
│   └── OpenTelemetry
│
└── Real Troubleshooting

    Troubleshooting Scenarios

─────────────────────────────────────────────
│ 3. Autoscaling ⭐⭐⭐⭐⭐
─────────────────────────────────────────────

    Horizontal Pod Autoscaler
    ├── Metrics Server
    ├── CPU Scaling
    ├── Memory Scaling
    ├── HPA Algorithm
    ├── YAML
    └── Production

    Vertical Pod Autoscaler
    ├── Recommendations
    ├── Auto Mode
    ├── Recreate Mode
    └── Limitations

    Cluster Autoscaler
    ├── Pending Pods
    ├── Node Groups
    ├── Scale Up
    ├── Scale Down
    └── Production

    KEDA
    ├── Event Driven
    ├── Kafka
    ├── RabbitMQ
    ├── Prometheus
    └── Azure Queues
| Component              | What it scales                                                                 |
| ---------------------- | ------------------------------------------------------------------------------ |
| **HPA**                | Number of **Pods** based on CPU, memory, or metrics                            |
| **VPA**                | CPU and memory **requests/limits** of a Pod  (increases size of pod)           |
| **Cluster Autoscaler** | Number of Kubernetes **worker nodes**                                          |
| **KEDA**               | Number of **Pods** based on external events (Kafka, RabbitMQ, SQS, Cron, etc.) |


─────────────────────────────────────────────
│ 4. Helm / Package Management ⭐⭐⭐⭐
─────────────────────────────────────────────

    Helm Basics
    Charts
    Repositories
    Values.yaml
    Templates
    Functions
    Helpers
    Dependencies
    Install
    Upgrade
    Rollback
    Hooks
    OCI Registries

    FluxCD
    Flux
      ├── Source Controller
      ├── Kustomize Controller
      ├── Helm Controller ⭐⭐⭐⭐⭐
      ├── Notification Controller


| Component                   | Responsibility                                               |
| --------------------------- | ------------------------------------------------------------ |
| GitHub                      | Stores source code, Helm charts, and GitOps manifests        |
| CI (Jenkins/GitHub Actions) | Builds, tests, scans, pushes images                          |
| JFrog Artifactory           | Stores Docker images and Helm charts                         |
| FluxCD                      | Watches Git and reconciles cluster state                     |
| Helm                        | Renders templates into Kubernetes manifests                  |
| Rancher                     | Manages Kubernetes clusters, users, RBAC, and infrastructure |
| Kubernetes                  | Runs the workloads                                           |
| Prometheus                  | Collects metrics                                             |
| Grafana                     | Visualizes metrics                                           |
| HPA                         | Scales Pods                                                  |
| Cluster Autoscaler          | Scales worker nodes                                          |


─────────────────────────────────────────────
│ 5. Backup & Disaster Recovery ⭐⭐⭐⭐
─────────────────────────────────────────────

    etcd
    ├── Backup
    ├── Restore
    └── Encryption

    PV Backups
    ├── Snapshots
    ├── Restore
    └── CSI

    Velero

    Disaster Recovery
    ├── Single Cluster
    ├── Multi Region
    └── Production

─────────────────────────────────────────────
│ 6. Multi-Cluster ⭐⭐
─────────────────────────────────────────────

    kubeconfig
    Contexts
    Multiple Clusters
    kubectl config
    Federation
    GitOps
    Fleet Management

─────────────────────────────────────────────
│ 7. CKA Troubleshooting ⭐⭐⭐⭐⭐
─────────────────────────────────────────────

    Pod Issues
    Network Issues
    DNS Issues
    Storage Issues
    Security Issues
    Scheduler Issues
    Resource Issues

─────────────────────────────────────────────
│ 8. Production Architecture ⭐⭐⭐⭐⭐
─────────────────────────────────────────────

    Complete Flow

    User

      ↓

    API Server

      ↓

    Scheduler

      ↓

    Node

      ↓

    kubelet

      ↓

    Runtime

      ↓

    Networking

      ↓

    Storage

      ↓

    Security

      ↓

    Monitoring

      ↓

    Autoscaling


```
