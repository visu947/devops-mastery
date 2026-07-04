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

# Module 2 - Workloads ✅

We covered:

✅ Pod
├── Concept: Smallest deployable unit in Kubernetes containing one or more containers.
├── Production: Usually one container per Pod (sidecars are common exceptions).
├── Best Practice: Never create standalone Pods in production.
├── Interview Tip: Pods are ephemeral; Deployments manage their lifecycle.
└── Questions I Asked
    Q. Should we create Pods directly?
    A. No. Use Deployments, StatefulSets, DaemonSets or Jobs.

---------------------------------------------------------

✅ ReplicaSet
├── Concept: Maintains the desired number of Pod replicas.
├── Production: Automatically created and managed by Deployments.
├── Best Practice: Rarely create ReplicaSets manually.
├── Interview Tip: ReplicaSet only manages Pod count.
└── Questions I Asked
    Q. Do we create ReplicaSets manually?
    A. Usually no. Deployments create and manage them.

---------------------------------------------------------

✅ Deployment
├── Concept: Manages ReplicaSets and provides rolling updates.
├── Production: Most stateless applications run as Deployments.
├── Best Practice: Always use RollingUpdate strategy.
├── Interview Tip: Deployment → ReplicaSet → Pods.
└── Questions I Asked
    Q. If a Pod dies, who recreates it?
    A. ReplicaSet recreates the Pod; Deployment manages the ReplicaSet.

    Q. Can Deployment manage Stateful applications?
    A. No. Stateful applications should use StatefulSets.

---------------------------------------------------------

✅ DaemonSet
├── Concept: Ensures one Pod runs on every selected node.
├── Production: Used for monitoring, logging and networking agents.
├── Best Practice: Use Node Selectors/Taints if only certain nodes require it.
├── Interview Tip: New node joins → DaemonSet automatically schedules a Pod.
└── Questions I Asked
    Q. Give real examples?
    A. Prometheus Node Exporter, Fluent Bit, Calico, Cilium, Security Agents.

---------------------------------------------------------

✅ StatefulSet
├── Concept: Manages stateful applications with stable identity and storage.
├── Production: Used for databases and clustered applications.
├── Best Practice: Use PersistentVolumes with StatefulSets.
├── Interview Tip: Pods are created and deleted in order.
└── Questions I Asked
    Q. Why not Deployment for PostgreSQL?
    A. Deployment Pods are interchangeable. Databases require stable identity and persistent storage.

    Q. Can each Pod have its own volume?
    A. Yes. Each StatefulSet replica gets its own PersistentVolumeClaim.

---------------------------------------------------------

✅ Job
├── Concept: Runs a task until it completes successfully.
├── Production: Database migrations, backups, data imports.
├── Best Practice: Jobs should be idempotent whenever possible.
├── Interview Tip: Job finishes once the task completes successfully.
└── Questions I Asked
    Q. What happens if the Pod fails?
    A. Kubernetes retries until the Job succeeds or reaches the backoff limit.

---------------------------------------------------------

✅ CronJob
├── Concept: Runs Jobs on a schedule.
├── Production: Nightly backups, cleanup tasks, reports.
├── Best Practice: Configure concurrencyPolicy to avoid overlapping executions.
├── Interview Tip: CronJob creates Jobs; Jobs create Pods.
└── Questions I Asked
    Q. Does CronJob run Pods directly?
    A. No. CronJob creates a Job, which then creates the Pod.

---------------------------------------------------------

Quick Comparison

| Resource     | Primary Purpose                           |
|--------------|-------------------------------------------|
| Pod          | Runs one or more containers               |
| ReplicaSet   | Maintains desired Pod count               |
| Deployment   | Rolling updates & stateless applications  |
| DaemonSet    | One Pod per node                          |
| StatefulSet  | Stateful workloads with stable identity   |
| Job          | One-time task                             |
| CronJob      | Scheduled task                            |

Production Best Practices

✔ Don't create standalone Pods.
✔ Use Deployments for stateless applications.
✔ Use StatefulSets for databases.
✔ Use DaemonSets for cluster-wide agents.
✔ Use Jobs for one-time operations.
✔ Use CronJobs for scheduled operations.

Memory Trick

Pod
    │
ReplicaSet
    │
Deployment

CronJob
    │
Job
    │
Pod

StatefulSet
    │
Stable Pod + Stable Storage

DaemonSet
    │
One Pod per Node

Understanding: 100%

Understanding: 100%

# Module 3 - Scheduling ✅

We covered:

✅ Labels
├── Concept: Key-value pairs attached to Kubernetes objects.
├── Production: Used for application grouping, environment, team and version.
├── Best Practice: Use consistent labeling across all workloads.
├── Interview Tip: Labels identify objects; Selectors find them.
└── Questions I Asked
    Q. Can Labels be changed after deployment?
    A. Yes. Labels are mutable and can be updated.

---------------------------------------------------------

✅ Selectors
├── Concept: Used to select Kubernetes objects based on Labels.
├── Production: Services and ReplicaSets use Selectors to find Pods.
├── Best Practice: Keep Labels and Selectors consistent.
├── Interview Tip: Wrong Selector = No Pods matched.
└── Questions I Asked
    Q. Does Service know Pod IPs directly?
    A. No. Service uses Label Selectors to discover Pods.

---------------------------------------------------------

✅ NodeSelector
├── Concept: Schedules Pods only onto nodes with matching Labels.
├── Production: GPU nodes, SSD nodes, Database nodes.
├── Best Practice: Use for simple scheduling requirements.
├── Interview Tip: Exact label match required.
└── Questions I Asked
    Q. What happens if no matching node exists?
    A. Pod remains Pending.

---------------------------------------------------------

✅ Node Affinity
├── Concept: Advanced scheduling based on node labels.
├── Production: Preferred or required node placement.
├── Best Practice: Prefer "preferredDuringScheduling" when possible.
├── Interview Tip: More flexible than NodeSelector.
└── Questions I Asked
    Q. Difference between NodeSelector and Node Affinity?
    A. NodeSelector supports exact matching only. Node Affinity supports expressions and preferred rules.

---------------------------------------------------------

✅ Pod Affinity
├── Concept: Schedule Pods close to other Pods.
├── Production: Microservices with heavy communication.
├── Best Practice: Use when low latency between services is required.
├── Interview Tip: Improves communication performance.
└── Questions I Asked
    Q. Example?
    A. Web Pod prefers same node/zone as Cache Pod.

---------------------------------------------------------

✅ Pod Anti-Affinity
├── Concept: Prevent Pods from running together.
├── Production: High Availability.
├── Best Practice: Spread replicas across nodes or zones.
├── Interview Tip: Prevents a single node failure from affecting all replicas.
└── Questions I Asked
    Q. Why use Anti-Affinity?
    A. To improve fault tolerance and availability.

---------------------------------------------------------

✅ Taints
├── Concept: Prevent Pods from being scheduled onto specific nodes.
├── Production: Dedicated GPU, Database or Infrastructure nodes.
├── Best Practice: Taint special-purpose nodes.
├── Interview Tip: Taints repel Pods.
└── Questions I Asked
    Q. What happens if a Pod has no matching Toleration?
    A. Scheduler will not place it on the tainted node.

---------------------------------------------------------

✅ Tolerations
├── Concept: Allow Pods to run on tainted nodes.
├── Production: Monitoring, Infrastructure and GPU workloads.
├── Best Practice: Add only when necessary.
├── Interview Tip: Toleration allows scheduling but doesn't force it.
└── Questions I Asked
    Q. Does Toleration guarantee scheduling?
    A. No. It only removes the restriction imposed by the Taint.

---------------------------------------------------------

✅ PriorityClass
├── Concept: Assigns scheduling priority to Pods.
├── Production: Critical system Pods receive higher priority.
├── Best Practice: Reserve highest priorities for platform components.
├── Interview Tip: Higher priority Pods can preempt lower priority Pods.
└── Questions I Asked
    Q. What happens if the cluster is full?
    A. Lower priority Pods may be preempted to schedule higher priority Pods.

---------------------------------------------------------

Quick Comparison

| Resource          | Primary Purpose                          |
|-------------------|------------------------------------------|
| Labels            | Identify Kubernetes Objects              |
| Selectors         | Find Objects using Labels                |
| NodeSelector      | Simple Node Scheduling                   |
| Node Affinity     | Advanced Node Scheduling                 |
| Pod Affinity      | Place Pods Together                      |
| Pod AntiAffinity  | Spread Pods Apart                        |
| Taints            | Keep Pods Away                           |
| Tolerations       | Allow Pods onto Tainted Nodes            |
| PriorityClass     | Decide Scheduling Priority               |

Production Best Practices

✔ Label everything consistently.
✔ Use Node Affinity instead of NodeSelector for flexibility.
✔ Use Pod Anti-Affinity for highly available applications.
✔ Taint infrastructure nodes.
✔ Give critical platform Pods higher PriorityClass.
✔ Don't overuse required affinity rules.

Memory Trick

Labels
     │
Selectors

Node
├── NodeSelector
└── Node Affinity

Pods
├── Pod Affinity
└── Pod Anti-Affinity

Taints
     │
Tolerations

PriorityClass
     │
Preemption

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
