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

# Module 4 - Networking ✅

We covered:

✅ CNI (Container Network Interface)
├── Concept: Standard interface that provides Pod networking.
├── Production: Every cluster requires one CNI plugin.
├── Best Practice: Use Calico or Cilium for production.
├── Interview Tip: Kubernetes provides the interface (CNI), plugins provide networking.
└── Questions I Asked
    Q. Does Rancher provide CNI?
    A. No. Rancher installs/configures a CNI plugin (Calico, Cilium, etc.).

---------------------------------------------------------

✅ Calico
├── Concept: CNI plugin providing networking and NetworkPolicies.
├── Production: One of the most widely used enterprise CNIs.
├── Best Practice: Use when strong NetworkPolicy support is required.
├── Interview Tip: Uses iptables/eBPF depending on configuration.

---------------------------------------------------------

✅ Cilium
├── Concept: eBPF-based CNI with advanced networking and security.
├── Production: High-performance networking and observability.
├── Best Practice: Preferred for modern Kubernetes platforms.
├── Interview Tip: eBPF reduces dependence on iptables.

---------------------------------------------------------

✅ Flannel
├── Concept: Lightweight CNI providing Pod networking only.
├── Production: Common in small or learning environments.
├── Best Practice: Pair with another solution if NetworkPolicies are needed.
├── Interview Tip: Simpler than Calico or Cilium.

---------------------------------------------------------

✅ Pod Networking
├── Concept: Every Pod receives its own IP address.
├── Production: Pods communicate directly without NAT.
├── Best Practice: Never assume Pod IPs are permanent.
├── Interview Tip: Pod IP changes when Pod is recreated.
└── Questions I Asked
    Q. Can Pods communicate directly?
    A. Yes. Kubernetes networking allows Pod-to-Pod communication.

---------------------------------------------------------

✅ ClusterIP
├── Concept: Internal Service accessible only inside the cluster.
├── Production: Default Service type.
├── Best Practice: Use for internal microservice communication.
├── Interview Tip: Not accessible outside the cluster.
└── Questions I Asked
    Q. Is ClusterIP reachable from outside?
    A. No. Only from within the cluster.

---------------------------------------------------------

✅ NodePort
├── Concept: Exposes Service on a static port of every node.
├── Production: Mostly used for testing or behind external load balancers.
├── Best Practice: Avoid exposing production applications directly.
├── Interview Tip: Opens the same port on every worker node.

---------------------------------------------------------

✅ LoadBalancer
├── Concept: Creates an external cloud load balancer.
├── Production: AWS ALB/NLB, Azure Load Balancer, GCP Load Balancer.
├── Best Practice: Preferred cloud-native external access.
├── Interview Tip: Requires cloud provider integration.
└── Questions I Asked
    Q. Who creates the LoadBalancer?
    A. Cloud Controller Manager communicates with the cloud provider.

---------------------------------------------------------

✅ Ingress
├── Concept: HTTP/HTTPS routing for multiple Services.
├── Production: Single entry point for applications.
├── Best Practice: Use Ingress instead of multiple LoadBalancers.
├── Interview Tip: Ingress requires an Ingress Controller.
└── Questions I Asked
    Q. Does Ingress route traffic by itself?
    A. No. The Ingress Controller implements the routing.

---------------------------------------------------------

✅ Ingress Controller
├── Concept: Watches Ingress resources and configures routing.
├── Production: NGINX, Traefik, HAProxy, AWS Load Balancer Controller.
├── Best Practice: Deploy at least two replicas for HA.
├── Interview Tip: Similar to a Kubernetes Operator.
└── Questions I Asked
    Q. Who develops the Ingress Controller?
    A. Platform team installs and manages it; application teams only create Ingress resources.

---------------------------------------------------------

✅ CoreDNS
├── Concept: Kubernetes DNS server.
├── Production: Resolves Service names to ClusterIPs.
├── Best Practice: Applications should use Service names instead of Pod IPs.
├── Interview Tip: CoreDNS replaces kube-dns.
└── Questions I Asked
    Q. How does payment.default resolve?
    A. CoreDNS returns the ClusterIP of the Service.

---------------------------------------------------------

✅ Endpoints
├── Concept: Stores the Pod IPs behind a Service.
├── Production: Automatically managed by Kubernetes.
├── Best Practice: Never create manually.
├── Interview Tip: Services forward traffic using Endpoints.

---------------------------------------------------------

✅ EndpointSlices
├── Concept: Scalable replacement for Endpoints.
├── Production: Used automatically in large clusters.
├── Best Practice: Required for thousands of Pods.
├── Interview Tip: Improves scalability and performance.

---------------------------------------------------------

✅ NetworkPolicies
├── Concept: Firewall rules for Pod-to-Pod communication.
├── Production: Implements Zero Trust networking.
├── Best Practice: Default deny, explicitly allow required traffic.
├── Interview Tip: Requires a CNI supporting NetworkPolicies.
└── Questions I Asked
    Q. Does Kubernetes enforce NetworkPolicies?
    A. No. The CNI plugin (Calico/Cilium) enforces them.

---------------------------------------------------------

Quick Comparison

| Resource            | Primary Purpose                       |
|--------------------|----------------------------------------|
| CNI                | Provides Pod Networking                |
| Calico             | Networking + NetworkPolicies           |
| Cilium             | eBPF Networking + Security             |
| Flannel            | Basic Pod Networking                   |
| ClusterIP          | Internal Service                       |
| NodePort           | External Access via Node Port          |
| LoadBalancer       | Cloud Load Balancer                    |
| Ingress            | HTTP/HTTPS Routing                     |
| Ingress Controller | Implements Ingress                     |
| CoreDNS            | Service Discovery                      |
| Endpoints          | Pod IP List                            |
| EndpointSlices     | Scalable Endpoints                     |
| NetworkPolicies    | Pod Firewall                           |

Production Best Practices

✔ Use ClusterIP for internal services.
✔ Use Ingress instead of multiple LoadBalancers.
✔ Never use Pod IPs directly.
✔ Use DNS (Service Names).
✔ Enable NetworkPolicies.
✔ Use Calico or Cilium in production.
✔ Deploy multiple Ingress Controller replicas.
✔ Monitor CoreDNS health.

Memory Trick

Internet
     │
LoadBalancer
     │
Ingress
     │
ClusterIP Service
     │
Endpoints
     │
Pods

CoreDNS
     │
Service Discovery

CNI
     │
Pod Networking

NetworkPolicy
     │
Traffic Control

Understanding: 98%
# Module 5 - Storage ✅

We covered:

✅ Volumes
├── Concept: Temporary storage attached to a Pod.
├── Production: Used for sharing data between containers.
├── Best Practice: Use only for ephemeral data.
├── Interview Tip: Deleted when Pod is deleted (except Persistent Volumes).

---------------------------------------------------------

✅ emptyDir
├── Concept: Temporary storage created when Pod starts.
├── Production: Cache, scratch space, shared files.
├── Best Practice: Never store important data.
├── Interview Tip: Removed when Pod is deleted.
└── Questions I Asked
    Q. Does emptyDir survive Pod restart?
    A. Container restart = Yes. Pod recreation = No.

---------------------------------------------------------

✅ hostPath
├── Concept: Mounts a directory from the worker node.
├── Production: Mostly for system/infrastructure Pods.
├── Best Practice: Avoid for application workloads.
├── Interview Tip: Tightly couples Pod to a node.

---------------------------------------------------------

✅ ConfigMap Volume
├── Concept: Mount configuration files into Pods.
├── Production: Application configs.
├── Best Practice: Store configuration only.
├── Interview Tip: Never store secrets here.

---------------------------------------------------------

✅ Secret Volume
├── Concept: Mount Kubernetes Secrets as files.
├── Production: Certificates, passwords, API keys.
├── Best Practice: Prefer External Secrets/Vault for production.
├── Interview Tip: Secret values are Base64 encoded, not encrypted.

---------------------------------------------------------

✅ PersistentVolume (PV)
├── Concept: Actual storage resource.
├── Production: Database and application data.
├── Best Practice: Provision dynamically using StorageClass.
├── Interview Tip: Cluster resource.

---------------------------------------------------------

✅ PersistentVolumeClaim (PVC)
├── Concept: Request for storage.
├── Production: Applications consume PVCs instead of PVs.
├── Best Practice: Applications should never reference PV directly.
├── Interview Tip: Namespace resource.
└── Questions I Asked
    Q. Does Pod mount PV directly?
    A. No. Pod mounts PVC, PVC binds to PV.

---------------------------------------------------------

✅ StorageClass
├── Concept: Defines how storage is dynamically provisioned.
├── Production: SSD, HDD, Premium, GP3, etc.
├── Best Practice: Make one StorageClass default.
├── Interview Tip: Enables Dynamic Provisioning.
└── Questions I Asked
    Q. Static vs Dynamic Provisioning?
    A. Static = Admin creates PV. Dynamic = Kubernetes creates PV automatically.

---------------------------------------------------------

✅ CSI Driver (Container Storage Interface)
├── Concept: Standard interface between Kubernetes and storage.
├── Production: AWS EBS, Azure Disk, vSphere, Longhorn, Ceph.
├── Best Practice: Use vendor-supported CSI drivers.
├── Interview Tip: CSI is for Storage, CNI is for Networking.
└── Questions I Asked
    Q. Does Rancher provide CSI?
    A. No. Rancher manages clusters; storage backend provides the CSI driver.

---------------------------------------------------------

✅ Static Provisioning
├── Concept: Administrator manually creates PVs.
├── Production: Legacy environments.
├── Best Practice: Use only when required.
├── Interview Tip: Manual lifecycle management.

---------------------------------------------------------

✅ Dynamic Provisioning
├── Concept: Kubernetes automatically creates PVs.
├── Production: Most modern clusters.
├── Best Practice: Use StorageClasses.
├── Interview Tip: Recommended approach.

---------------------------------------------------------

✅ StatefulSet Storage
├── Concept: Each Pod receives its own PersistentVolume.
├── Production: PostgreSQL, MySQL, MongoDB.
├── Best Practice: Never share database volumes.
├── Interview Tip: Stable identity + Stable storage.
└── Questions I Asked
    Q. Why StatefulSet instead of Deployment?
    A. Databases require stable hostname and dedicated storage.

---------------------------------------------------------

✅ Access Modes
├── ReadWriteOnce (RWO)
├── ReadOnlyMany (ROX)
├── ReadWriteMany (RWX)
├── ReadWriteOncePod (RWOP)
├── Production: Choose based on storage backend.
├── Interview Tip: Not every storage supports RWX.
└── Questions I Asked
    Q. Does AWS EBS support RWX?
    A. No. EBS supports RWO. RWX typically requires EFS, CephFS, NFS, etc.

---------------------------------------------------------

✅ Reclaim Policy
├── Retain
├── Delete
├── Recycle (Deprecated)
├── Production: Retain for important databases.
├── Interview Tip: Controls what happens after PVC deletion.
└── Questions I Asked
    Q. Which policy is safest for production databases?
    A. Retain.

---------------------------------------------------------

✅ Volume Expansion
├── Concept: Increase PVC size without recreating it.
├── Production: StorageClass must allow expansion.
├── Best Practice: Verify application filesystem expansion.
├── Interview Tip: Not all CSI drivers support expansion.

---------------------------------------------------------

✅ Volume Binding Mode
├── Immediate
├── WaitForFirstConsumer
├── Production: WaitForFirstConsumer avoids wrong zone allocation.
├── Interview Tip: Commonly used in cloud environments.

---------------------------------------------------------

✅ VolumeSnapshot
├── Concept: Point-in-time snapshot of a PersistentVolume.
├── Production: Backup and Disaster Recovery.
├── Best Practice: Integrate with Velero.
├── Interview Tip: Snapshot != Backup unless stored externally.

---------------------------------------------------------

✅ VolumeSnapshotClass
├── Concept: Defines snapshot provider and parameters.
├── Production: Maps to CSI Driver.
├── Interview Tip: Similar to StorageClass but for snapshots.

---------------------------------------------------------

✅ VolumeSnapshotContent
├── Concept: Represents the actual snapshot in the storage backend.
├── Production: Managed automatically.
├── Interview Tip: Similar relationship as PV ↔ PVC.

---------------------------------------------------------

✅ Restore
├── Concept: Create a new PVC from a VolumeSnapshot.
├── Production: Disaster Recovery.
├── Best Practice: Test restores regularly.
├── Interview Tip: Backups without restore testing are incomplete.

---------------------------------------------------------

Quick Comparison

| Resource               | Purpose                              |
|------------------------|--------------------------------------|
| Volume                 | Temporary Pod Storage                |
| emptyDir               | Ephemeral Storage                    |
| hostPath               | Node Storage                         |
| ConfigMap Volume       | Configuration Files                  |
| Secret Volume          | Sensitive Files                      |
| PV                     | Actual Storage                       |
| PVC                    | Storage Request                      |
| StorageClass           | Dynamic Provisioning                 |
| CSI                    | Storage Interface                    |
| StatefulSet            | Stable Storage                       |
| VolumeSnapshot         | Point-in-time Snapshot               |
| VolumeSnapshotClass    | Snapshot Configuration               |
| VolumeSnapshotContent  | Actual Snapshot                      |

Production Best Practices

✔ Use Dynamic Provisioning.
✔ Never mount PV directly.
✔ Use StatefulSets for databases.
✔ Use Retain for production databases.
✔ Use CSI drivers.
✔ Test restore procedures regularly.
✔ Monitor storage capacity.
✔ Use StorageClasses consistently.

Memory Trick

Pod
 │
PVC
 │
PV
 │
StorageClass
 │
CSI Driver
 │
Cloud Storage

Snapshot
 │
VolumeSnapshot
 │
Restore

Questions I Asked

Q. Does Rancher provide CSI?
A. No. Storage backend provides the CSI Driver.

Q. Can Velero snapshot PVs?
A. Yes. Through CSI drivers.

Q. Does Velero backup etcd?
A. No. It backs up Kubernetes objects via the API Server.

Q. Crash Consistent vs Application Consistent?
A. CSI Snapshot = Crash Consistent.
   Production DBs also use native backups (WAL, pg_dump, etc.).

Q. What should be backed up?
A.
• etcd (Cluster Metadata)
• Kubernetes Objects (Velero)
• Persistent Volumes (CSI)
• Database Native Backups

Understanding: 98%

# Module 6 - Security ✅

We covered:

=========================================================

✅ Users
├── Concept: External identities authenticated outside Kubernetes.
├── Production: Admins, DevOps Engineers, CI/CD Systems.
├── Best Practice: Integrate with OIDC/LDAP instead of certificates.
├── Interview Tip: Kubernetes does NOT manage Users internally.

---------------------------------------------------------

✅ Groups
├── Concept: Collection of Users.
├── Production: DevOps, Developers, QA, Security Teams.
├── Best Practice: Assign RBAC to Groups instead of individual Users.
├── Interview Tip: Simplifies permission management.

---------------------------------------------------------

✅ ServiceAccount
├── Concept: Identity used by Pods.
├── Production: Every application should use its own ServiceAccount.
├── Best Practice: Never use default ServiceAccount in production.
├── Interview Tip: Pods authenticate using ServiceAccounts.
└── Questions I Asked
    Q. Difference between User and ServiceAccount?
    A. Users are for humans; ServiceAccounts are for Pods.

---------------------------------------------------------

✅ Authentication
├── Concept: Verifies identity.
├── Production: OIDC, Certificates, ServiceAccount Tokens.
├── Best Practice: Use OIDC for enterprise authentication.
├── Interview Tip: Authentication answers "Who are you?"

---------------------------------------------------------

✅ OIDC
├── Concept: External identity provider integration.
├── Production: Azure AD, Okta, Keycloak, Google.
├── Best Practice: Centralize authentication.
├── Interview Tip: Most enterprises use OIDC.

---------------------------------------------------------

✅ Certificates
├── Concept: TLS certificates authenticate Kubernetes components.
├── Production: API Server, kubelet, etcd.
├── Best Practice: Rotate certificates before expiration.
├── Interview Tip: kubeadm manages certificates automatically.

---------------------------------------------------------

✅ RBAC
├── Concept: Controls permissions inside Kubernetes.
├── Production: Least Privilege Access.
├── Best Practice: Never grant cluster-admin unnecessarily.
├── Interview Tip: Authorization answers "What can you do?"
└── Questions I Asked
    Q. Authentication vs Authorization?
    A. Authentication verifies identity. RBAC authorizes actions.

---------------------------------------------------------

✅ Role
├── Concept: Namespace-level permissions.
├── Production: Application namespace access.
├── Best Practice: Prefer Role over ClusterRole when possible.
├── Interview Tip: Namespace scoped.

---------------------------------------------------------

✅ ClusterRole
├── Concept: Cluster-wide permissions.
├── Production: Platform components and administrators.
├── Best Practice: Grant only when cluster-wide access is required.
├── Interview Tip: Not limited to one namespace.

---------------------------------------------------------

✅ RoleBinding
├── Concept: Assigns Role to User, Group or ServiceAccount.
├── Production: Namespace access.
├── Best Practice: Use Groups instead of individual Users.
├── Interview Tip: Namespace scoped.

---------------------------------------------------------

✅ ClusterRoleBinding
├── Concept: Assigns ClusterRole cluster-wide.
├── Production: Platform administrators.
├── Best Practice: Limit usage carefully.
├── Interview Tip: Highest permission scope.

---------------------------------------------------------

✅ Admission Controllers
├── Concept: Validate or modify requests before persistence.
├── Production: Policy enforcement.
├── Best Practice: Enable required admission plugins.
├── Interview Tip: Executes before objects are stored.

---------------------------------------------------------

✅ Mutating Admission
├── Concept: Modifies Kubernetes objects.
├── Production: Inject sidecars, labels, annotations.
├── Best Practice: Keep mutations predictable.
├── Interview Tip: Runs before Validation.

---------------------------------------------------------

✅ Validating Admission
├── Concept: Accepts or rejects requests.
├── Production: Security and compliance.
├── Best Practice: Reject insecure workloads.
├── Interview Tip: Runs after Mutation.

---------------------------------------------------------

✅ Pod Security Admission
├── Concept: Enforces Pod security standards.
├── Production: Restricted, Baseline, Privileged.
├── Best Practice: Use Restricted policy whenever possible.
├── Interview Tip: Replaces PodSecurityPolicy.

---------------------------------------------------------

✅ SecurityContext
├── Concept: Defines container security settings.
├── Production: Non-root containers.
├── Best Practice: Drop unnecessary Linux capabilities.
├── Interview Tip: First place to harden workloads.
└── Questions I Asked
    Q. Why runAsNonRoot?
    A. Reduces privilege escalation risk.

---------------------------------------------------------

✅ Privileged Containers
├── Concept: Containers with host-level privileges.
├── Production: Only infrastructure components.
├── Best Practice: Avoid unless absolutely necessary.
├── Interview Tip: Large security risk.

---------------------------------------------------------

✅ Linux Capabilities
├── Concept: Fine-grained Linux privileges.
├── Production: Grant only required capabilities.
├── Best Practice: Drop ALL, then add only what is needed.
├── Interview Tip: Better than privileged containers.

---------------------------------------------------------

✅ readOnlyRootFilesystem
├── Concept: Makes container filesystem read-only.
├── Production: Prevents runtime modifications.
├── Best Practice: Enable wherever possible.
├── Interview Tip: Limits malware persistence.

---------------------------------------------------------

✅ Kubernetes Secrets
├── Concept: Stores sensitive information.
├── Production: Small secrets only.
├── Best Practice: Avoid storing production secrets directly.
├── Interview Tip: Base64 encoding is NOT encryption.
└── Questions I Asked
    Q. Are Kubernetes Secrets secure?
    A. Better than ConfigMaps, but use Vault for production.

---------------------------------------------------------

✅ Vault
├── Concept: External Secret Management.
├── Production: Dynamic credentials and secret rotation.
├── Best Practice: Never hardcode production credentials.
├── Interview Tip: Industry standard for enterprise secrets.
└── Questions I Asked
    Q. Why use Vault if Kubernetes has Secrets?
    A. Vault provides encryption, auditing, rotation and dynamic secrets.

---------------------------------------------------------

✅ External Secrets Operator
├── Concept: Synchronizes external secrets into Kubernetes.
├── Production: Vault, AWS Secrets Manager, Azure Key Vault.
├── Best Practice: Keep Kubernetes Secrets synchronized automatically.
├── Interview Tip: Kubernetes never stores master credentials.
└── Questions I Asked
    Q. Does ESO continuously sync?
    A. Yes. Secrets are automatically refreshed.

---------------------------------------------------------

✅ Vault Agent
├── Concept: Sidecar that retrieves secrets directly from Vault.
├── Production: Dynamic secret injection.
├── Best Practice: Avoid application code talking directly to Vault.
├── Interview Tip: Secrets can be injected without Kubernetes Secrets.

---------------------------------------------------------

✅ Secrets Store CSI Driver
├── Concept: Mounts external secrets directly into Pods.
├── Production: Vault, Azure Key Vault, AWS Secrets Manager.
├── Best Practice: Avoid persisting secrets inside Kubernetes.
├── Interview Tip: Secrets are mounted as files.
└── Questions I Asked
    Q. Difference between ESO and CSI Driver?
    A. ESO creates Kubernetes Secrets. CSI mounts secrets directly into Pods.

---------------------------------------------------------

✅ Secret Rotation
├── Concept: Automatically updates credentials.
├── Production: Database passwords, API keys, certificates.
├── Best Practice: Rotate secrets regularly.
├── Interview Tip: Applications should support secret reload.

---------------------------------------------------------

✅ Image Security
├── Concept: Secure container images.
├── Production: Scan every image before deployment.
├── Best Practice: Use immutable image tags.
├── Interview Tip: Never deploy latest in production.

---------------------------------------------------------

✅ Audit Logs
├── Concept: Records Kubernetes API activity.
├── Production: Security investigations and compliance.
├── Best Practice: Store logs centrally.
├── Interview Tip: Every API request can be audited.

---------------------------------------------------------

Quick Comparison

| Component | Purpose |
|-----------|---------|
| User | Human Identity |
| ServiceAccount | Pod Identity |
| Authentication | Verify Identity |
| Authorization (RBAC) | Verify Permissions |
| Vault | Secret Management |
| ESO | Sync Secrets |
| CSI Secret Store | Mount Secrets |
| SecurityContext | Container Security |
| Audit Logs | API Tracking |

Production Best Practices

✔ Use OIDC.
✔ Never use default ServiceAccount.
✔ Apply Least Privilege RBAC.
✔ Use Vault for production secrets.
✔ Enable Secret Rotation.
✔ Run containers as Non-Root.
✔ Use readOnlyRootFilesystem.
✔ Scan every container image.
✔ Enable Audit Logging.

Memory Trick

Authentication
        │
Who Are You?

Authorization
        │
What Can You Do?

Vault
        │
Secret Source

ESO
        │
Kubernetes Secret

CSI Secret Store
        │
Mounted Secret

Questions I Asked

Q. Vault vs Kubernetes Secrets?
A. Vault manages secrets. Kubernetes consumes them.

Q. Vault Agent vs External Secrets?
A. Vault Agent injects secrets directly.
   ESO syncs secrets into Kubernetes.

Q. ESO vs CSI Secret Store?
A. ESO creates Kubernetes Secrets.
   CSI mounts secrets directly without creating Kubernetes Secrets.

Q. Why Secret Rotation?
A. Limits impact of credential leaks.

Understanding: 95%

# Module 7 - Resource Management ✅

We covered:

=========================================================

✅ CPU
├── Concept: CPU is measured in cores (1000m = 1 CPU).
├── Production: Scheduler uses CPU Requests for scheduling.
├── Best Practice: Always define CPU Requests.
├── Interview Tip: CPU Limit exceeded = Throttling, NOT killing.
└── Questions I Asked
    Q. What happens if CPU exceeds Limit?
    A. Linux throttles CPU usage. Container keeps running.

---------------------------------------------------------

✅ CPU Requests
├── Concept: Minimum CPU guaranteed for a Pod.
├── Production: Used by Scheduler for node placement.
├── Best Practice: Set realistic Requests based on usage.
├── Interview Tip: Requests determine scheduling.

---------------------------------------------------------

✅ CPU Limits
├── Concept: Maximum CPU a container can consume.
├── Production: Prevents noisy neighbor issues.
├── Best Practice: Don't set Limits too low.
├── Interview Tip: CPU can burst until Limit.

---------------------------------------------------------

✅ CPU Throttling
├── Concept: Linux CFS limits CPU usage.
├── Production: High latency often indicates throttling.
├── Best Practice: Monitor throttled containers.
├── Interview Tip: CPU throttling ≠ Crash.
└── Questions I Asked
    Q. Does the application stop?
    A. No. It continues with reduced CPU time.

---------------------------------------------------------

✅ Memory
├── Concept: RAM allocated to a container.
├── Production: Memory cannot be throttled.
├── Best Practice: Define Requests and Limits.
├── Interview Tip: Memory Limit exceeded = OOMKilled.

---------------------------------------------------------

✅ Memory Requests
├── Concept: Guaranteed memory for scheduling.
├── Production: Scheduler uses Requests only.
├── Best Practice: Set based on normal workload.
├── Interview Tip: Requests reserve capacity.

---------------------------------------------------------

✅ Memory Limits
├── Concept: Maximum memory allowed.
├── Production: Prevents one Pod exhausting node memory.
├── Best Practice: Avoid unlimited memory.
├── Interview Tip: Exceeding Limit triggers OOMKilled.

---------------------------------------------------------

✅ OOMKilled
├── Concept: Container exceeded Memory Limit.
├── Production: Container restarted by Kubernetes.
├── Best Practice: Increase memory or optimize application.
├── Interview Tip: OOMKilled affects only that container.
└── Questions I Asked
    Q. What happens when memory exceeds Limit?
    A. Kernel kills the container immediately.

---------------------------------------------------------

✅ Scheduler Decisions
├── Concept: Scheduler considers Requests, not Limits.
├── Production: Requests determine node placement.
├── Best Practice: Never leave Requests undefined.
├── Interview Tip: Scheduler ignores actual runtime usage.
└── Questions I Asked
    Q. Does Scheduler use Limits?
    A. No. Only Requests.

---------------------------------------------------------

✅ Allocatable Resources
├── Concept: Resources available after Kubernetes reserves system resources.
├── Production: kubeReserved + systemReserved reduce available capacity.
├── Best Practice: Reserve resources for system components.
├── Interview Tip: Node Capacity ≠ Allocatable.
└── Questions I Asked
    Q. Does Kubernetes reserve resources by default?
    A. Yes. System components consume part of node resources.

    Q. Can we change reserved resources?
    A. Yes. kubeReserved and systemReserved are configurable.

---------------------------------------------------------

✅ Pending Pods
├── Concept: Pod waiting for scheduling.
├── Production: Usually insufficient CPU, Memory or constraints.
├── Best Practice: Check Events first.
├── Interview Tip: Pending means Pod never started.

---------------------------------------------------------

✅ QoS Classes
├── Guaranteed
│   ├── Requests = Limits
│   ├── Highest Priority
│   └── Last to be Evicted
│
├── Burstable
│   ├── Requests < Limits
│   ├── Can Burst
│   └── Medium Priority
│
├── BestEffort
│   ├── No Requests
│   ├── No Limits
│   └── First to be Evicted
│
├── Production: Use Guaranteed for critical workloads.
├── Best Practice: Avoid BestEffort in production.
├── Interview Tip: QoS affects Eviction order.
└── Questions I Asked
    Q. If LimitRange sets Requests only, what QoS?
    A. Burstable.

    Q. If neither Requests nor Limits exist?
    A. BestEffort.

---------------------------------------------------------

✅ Evictions
├── Concept: Kubernetes removes Pods during node resource pressure.
├── Production: Protects node stability.
├── Best Practice: Define Requests to avoid early eviction.
├── Interview Tip: Different from OOMKilled.
└── Questions I Asked
    Q. OOMKilled vs Eviction?
    A.
      OOMKilled → Container exceeded its Memory Limit.
      Eviction → Node ran out of resources.

    Q. Eviction Order?
    A.
      BestEffort
          ↓
      Burstable
          ↓
      Guaranteed

---------------------------------------------------------

✅ ResourceQuota
├── Concept: Limits total namespace resource consumption.
├── Production: Prevents one team consuming all cluster resources.
├── Best Practice: Apply per namespace.
├── Interview Tip: Namespace-level control.
└── Questions I Asked
    Q. Production environments use ResourceQuota?
    A. Yes. Almost every multi-team cluster.

---------------------------------------------------------

✅ LimitRange
├── Concept: Sets default/min/max Requests and Limits.
├── Production: Prevents Pods without resource definitions.
├── Best Practice: Configure with ResourceQuota.
├── Interview Tip: Namespace-level defaults.
└── Questions I Asked
    Q. Best practice?
    A.
      ✔ Requests
      ✔ Limits
      ✔ ResourceQuota
      ✔ LimitRange

---------------------------------------------------------

Quick Comparison

| Component | Purpose |
|-----------|---------|
| CPU Request | Scheduling |
| CPU Limit | Throttling |
| Memory Request | Scheduling |
| Memory Limit | OOMKilled |
| ResourceQuota | Namespace Limits |
| LimitRange | Default Requests/Limits |
| Guaranteed | Highest QoS |
| Burstable | Medium QoS |
| BestEffort | Lowest QoS |
| Eviction | Node Protection |

Production Best Practices

✔ Always define Requests.
✔ Define Memory Limits.
✔ Avoid unlimited CPU/Memory.
✔ Configure ResourceQuota.
✔ Configure LimitRange.
✔ Use Guaranteed QoS for critical applications.
✔ Monitor OOMKilled events.
✔ Monitor CPU Throttling.
✔ Keep Allocatable resources in mind.

Memory Trick

CPU
│
├── Request → Scheduler
└── Limit → Throttling

Memory
│
├── Request → Scheduler
└── Limit → OOMKilled

Node Full
│
Eviction
│
BestEffort
↓
Burstable
↓
Guaranteed

Questions I Asked

Q. Does Kubernetes reserve resources for itself?
A. Yes. kubeReserved/systemReserved reduce Allocatable resources.

Q. Can reserved resources be changed?
A. Yes. They are configurable on worker nodes.

Q. Does Scheduler use actual CPU usage?
A. No. Scheduler only evaluates Requests.

Q. What happens if all node memory is exhausted?
A. Kubernetes performs Evictions.

Q. Which QoS gets evicted first?
A. BestEffort → Burstable → Guaranteed.

Q. Is it best practice to use Requests, Limits, ResourceQuota and LimitRange together?
A. Yes. That's the recommended production setup.

Understanding: 100%

# Module 7 - Resource Management ✅

We covered:

=========================================================

✅ CPU
├── Concept: CPU is measured in cores (1000m = 1 CPU).
├── Production: Scheduler uses CPU Requests for scheduling.
├── Best Practice: Always define CPU Requests.
├── Interview Tip: CPU Limit exceeded = Throttling, NOT killing.
└── Questions I Asked
    Q. What happens if CPU exceeds Limit?
    A. Linux throttles CPU usage. Container keeps running.

---------------------------------------------------------

✅ CPU Requests
├── Concept: Minimum CPU guaranteed for a Pod.
├── Production: Used by Scheduler for node placement.
├── Best Practice: Set realistic Requests based on usage.
├── Interview Tip: Requests determine scheduling.

---------------------------------------------------------

✅ CPU Limits
├── Concept: Maximum CPU a container can consume.
├── Production: Prevents noisy neighbor issues.
├── Best Practice: Don't set Limits too low.
├── Interview Tip: CPU can burst until Limit.

---------------------------------------------------------

✅ CPU Throttling
├── Concept: Linux CFS limits CPU usage.
├── Production: High latency often indicates throttling.
├── Best Practice: Monitor throttled containers.
├── Interview Tip: CPU throttling ≠ Crash.
└── Questions I Asked
    Q. Does the application stop?
    A. No. It continues with reduced CPU time.

---------------------------------------------------------

✅ Memory
├── Concept: RAM allocated to a container.
├── Production: Memory cannot be throttled.
├── Best Practice: Define Requests and Limits.
├── Interview Tip: Memory Limit exceeded = OOMKilled.

---------------------------------------------------------

✅ Memory Requests
├── Concept: Guaranteed memory for scheduling.
├── Production: Scheduler uses Requests only.
├── Best Practice: Set based on normal workload.
├── Interview Tip: Requests reserve capacity.

---------------------------------------------------------

✅ Memory Limits
├── Concept: Maximum memory allowed.
├── Production: Prevents one Pod exhausting node memory.
├── Best Practice: Avoid unlimited memory.
├── Interview Tip: Exceeding Limit triggers OOMKilled.

---------------------------------------------------------

✅ OOMKilled
├── Concept: Container exceeded Memory Limit.
├── Production: Container restarted by Kubernetes.
├── Best Practice: Increase memory or optimize application.
├── Interview Tip: OOMKilled affects only that container.
└── Questions I Asked
    Q. What happens when memory exceeds Limit?
    A. Kernel kills the container immediately.

---------------------------------------------------------

✅ Scheduler Decisions
├── Concept: Scheduler considers Requests, not Limits.
├── Production: Requests determine node placement.
├── Best Practice: Never leave Requests undefined.
├── Interview Tip: Scheduler ignores actual runtime usage.
└── Questions I Asked
    Q. Does Scheduler use Limits?
    A. No. Only Requests.

---------------------------------------------------------

✅ Allocatable Resources
├── Concept: Resources available after Kubernetes reserves system resources.
├── Production: kubeReserved + systemReserved reduce available capacity.
├── Best Practice: Reserve resources for system components.
├── Interview Tip: Node Capacity ≠ Allocatable.
└── Questions I Asked
    Q. Does Kubernetes reserve resources by default?
    A. Yes. System components consume part of node resources.

    Q. Can we change reserved resources?
    A. Yes. kubeReserved and systemReserved are configurable.

---------------------------------------------------------

✅ Pending Pods
├── Concept: Pod waiting for scheduling.
├── Production: Usually insufficient CPU, Memory or constraints.
├── Best Practice: Check Events first.
├── Interview Tip: Pending means Pod never started.

---------------------------------------------------------

✅ QoS Classes
├── Guaranteed
│   ├── Requests = Limits
│   ├── Highest Priority
│   └── Last to be Evicted
│
├── Burstable
│   ├── Requests < Limits
│   ├── Can Burst
│   └── Medium Priority
│
├── BestEffort
│   ├── No Requests
│   ├── No Limits
│   └── First to be Evicted
│
├── Production: Use Guaranteed for critical workloads.
├── Best Practice: Avoid BestEffort in production.
├── Interview Tip: QoS affects Eviction order.
└── Questions I Asked
    Q. If LimitRange sets Requests only, what QoS?
    A. Burstable.

    Q. If neither Requests nor Limits exist?
    A. BestEffort.

---------------------------------------------------------

✅ Evictions
├── Concept: Kubernetes removes Pods during node resource pressure.
├── Production: Protects node stability.
├── Best Practice: Define Requests to avoid early eviction.
├── Interview Tip: Different from OOMKilled.
└── Questions I Asked
    Q. OOMKilled vs Eviction?
    A.
      OOMKilled → Container exceeded its Memory Limit.
      Eviction → Node ran out of resources.

    Q. Eviction Order?
    A.
      BestEffort
          ↓
      Burstable
          ↓
      Guaranteed

---------------------------------------------------------

✅ ResourceQuota
├── Concept: Limits total namespace resource consumption.
├── Production: Prevents one team consuming all cluster resources.
├── Best Practice: Apply per namespace.
├── Interview Tip: Namespace-level control.
└── Questions I Asked
    Q. Production environments use ResourceQuota?
    A. Yes. Almost every multi-team cluster.

---------------------------------------------------------

✅ LimitRange
├── Concept: Sets default/min/max Requests and Limits.
├── Production: Prevents Pods without resource definitions.
├── Best Practice: Configure with ResourceQuota.
├── Interview Tip: Namespace-level defaults.
└── Questions I Asked
    Q. Best practice?
    A.
      ✔ Requests
      ✔ Limits
      ✔ ResourceQuota
      ✔ LimitRange

---------------------------------------------------------

Quick Comparison

| Component | Purpose |
|-----------|---------|
| CPU Request | Scheduling |
| CPU Limit | Throttling |
| Memory Request | Scheduling |
| Memory Limit | OOMKilled |
| ResourceQuota | Namespace Limits |
| LimitRange | Default Requests/Limits |
| Guaranteed | Highest QoS |
| Burstable | Medium QoS |
| BestEffort | Lowest QoS |
| Eviction | Node Protection |

Production Best Practices

✔ Always define Requests.
✔ Define Memory Limits.
✔ Avoid unlimited CPU/Memory.
✔ Configure ResourceQuota.
✔ Configure LimitRange.
✔ Use Guaranteed QoS for critical applications.
✔ Monitor OOMKilled events.
✔ Monitor CPU Throttling.
✔ Keep Allocatable resources in mind.

Memory Trick

CPU
│
├── Request → Scheduler
└── Limit → Throttling

Memory
│
├── Request → Scheduler
└── Limit → OOMKilled

Node Full
│
Eviction
│
BestEffort
↓
Burstable
↓
Guaranteed

Questions I Asked

Q. Does Kubernetes reserve resources for itself?
A. Yes. kubeReserved/systemReserved reduce Allocatable resources.

Q. Can reserved resources be changed?
A. Yes. They are configurable on worker nodes.

Q. Does Scheduler use actual CPU usage?
A. No. Scheduler only evaluates Requests.

Q. What happens if all node memory is exhausted?
A. Kubernetes performs Evictions.

Q. Which QoS gets evicted first?
A. BestEffort → Burstable → Guaranteed.

Q. Is it best practice to use Requests, Limits, ResourceQuota and LimitRange together?
A. Yes. That's the recommended production setup.

Understanding: 100%

# Module 8 - Observability & Troubleshooting ✅

We covered:

=========================================================

✅ Logs
├── Concept: View application and container logs.
├── Production: First step during application failures.
├── Best Practice: Centralize logs (ELK/Loki/Splunk).
├── Interview Tip: Logs show application behavior, not cluster state.
└── Questions I Asked
    Q. Which should I check first?
    A. kubectl logs, then kubectl describe.

---------------------------------------------------------

✅ kubectl logs
├── Concept: Display container logs.
├── Production: Troubleshoot application issues.
├── Best Practice: Use --previous for crashed containers.
├── Interview Tip: Reads stdout/stderr only.

Useful Commands

kubectl logs <pod>
kubectl logs -f <pod>
kubectl logs --previous <pod>

---------------------------------------------------------

✅ Previous Logs
├── Concept: Shows logs from previous container instance.
├── Production: Useful after CrashLoopBackOff.
├── Best Practice: Always check previous logs after restart.
├── Interview Tip: Current logs may be empty after restart.

---------------------------------------------------------

✅ Multi-Container Pods
├── Concept: View logs from specific container.
├── Production: Sidecars (Istio, Vault Agent, FluentBit).
├── Best Practice: Always specify container name.
├── Interview Tip: kubectl defaults to first container.

Useful Command

kubectl logs <pod> -c <container>

---------------------------------------------------------

✅ Describe
├── Concept: Displays Kubernetes object details and events.
├── Production: Check scheduling failures and probe failures.
├── Best Practice: Always inspect Events section.
├── Interview Tip: Most Kubernetes issues appear in Events.

Useful Command

kubectl describe pod <pod>

---------------------------------------------------------

✅ Events
├── Concept: Timeline of Kubernetes actions.
├── Production: Scheduling, OOMKilled, Probe Failures.
├── Best Practice: Check newest events first.
├── Interview Tip: Fastest way to identify cluster problems.

---------------------------------------------------------

✅ kubectl exec
├── Concept: Execute commands inside a container.
├── Production: Validate connectivity and configuration.
├── Best Practice: Use for debugging only.
├── Interview Tip: Doesn't survive Pod recreation.

Useful Commands

kubectl exec -it <pod> -- sh

kubectl exec -it <pod> -- bash

---------------------------------------------------------

✅ kubectl cp
├── Concept: Copy files between local machine and Pod.
├── Production: Export logs, import configs.
├── Best Practice: Avoid modifying production Pods.
├── Interview Tip: Useful for collecting diagnostics.

---------------------------------------------------------

✅ kubectl debug
├── Concept: Launch temporary debugging container.
├── Production: Distroless container troubleshooting.
├── Best Practice: Remove debug containers after use.
├── Interview Tip: Doesn't modify application image.

---------------------------------------------------------

✅ Ephemeral Containers
├── Concept: Temporary containers used only for debugging.
├── Production: Troubleshoot minimal images.
├── Best Practice: Never use as permanent containers.
├── Interview Tip: Created only for debugging sessions.

---------------------------------------------------------

✅ Metrics Server
├── Concept: Collects CPU and Memory usage from kubelets.
├── Production: Required for HPA and kubectl top.
├── Best Practice: Install in every production cluster.
├── Interview Tip: Metrics Server stores short-term metrics only.
└── Questions I Asked
    Q. Why does HPA need Metrics Server?
    A. Kubernetes doesn't calculate resource usage itself.
       Metrics Server provides CPU and Memory metrics.

    Q. Does Kubernetes scale automatically?
    A. No. HPA needs Metrics Server.

---------------------------------------------------------

✅ kubectl top
├── Concept: Displays live CPU and Memory usage.
├── Production: Quick resource inspection.
├── Best Practice: Verify Metrics Server is running.
├── Interview Tip: Doesn't provide historical metrics.

Useful Commands

kubectl top pod

kubectl top node

---------------------------------------------------------

✅ Liveness Probe
├── Concept: Checks whether application is alive.
├── Production: Restarts unhealthy containers.
├── Best Practice: Don't make probes too aggressive.
├── Interview Tip: Failure restarts container.
└── Questions I Asked
    Q. What happens if Liveness fails?
    A. kubelet restarts the container.

---------------------------------------------------------

✅ Readiness Probe
├── Concept: Determines if Pod can receive traffic.
├── Production: Prevents traffic to unhealthy Pods.
├── Best Practice: Configure before exposing applications.
├── Interview Tip: Failure removes Pod from Service.
└── Questions I Asked
    Q. What happens if Readiness fails?
    A. Pod continues running but is removed from Service endpoints.

---------------------------------------------------------

✅ Startup Probe
├── Concept: Gives slow-starting applications time to initialize.
├── Production: Java/Spring Boot applications.
├── Best Practice: Use for long startup times.
├── Interview Tip: Prevents premature restarts.

---------------------------------------------------------

Probe Summary

Startup
↓

Application Starting

Failure → Restart

----------------------------

Liveness
↓

Application Alive?

Failure → Restart

----------------------------

Readiness
↓

Ready for Traffic?

Failure → Removed from Service

---------------------------------------------------------

✅ Prometheus
├── Concept: Collects and stores time-series metrics.
├── Production: Monitoring and alerting.
├── Best Practice: Scrape only required metrics.
├── Interview Tip: Pull model, not Push.
└── Questions I Asked
    Q. How does Prometheus collect metrics?
    A. Periodically scrapes HTTP /metrics endpoints.

    Q. Does application expose metrics?
    A. Yes. Application team usually implements /metrics.

    Q. Does Prometheus collect system metrics too?
    A. Yes. Through kubelet/cAdvisor and Node Exporter.

---------------------------------------------------------

✅ Application Metrics
├── Concept: Business and application-specific metrics.
├── Production: HTTP Requests, JVM, Cache, Queue Length.
├── Best Practice: Follow Prometheus metric naming.
├── Interview Tip: Exposed through /metrics endpoint.

Examples

http_requests_total

http_request_duration_seconds

jvm_memory_used_bytes

---------------------------------------------------------

✅ kubelet / cAdvisor Metrics
├── Concept: Container resource metrics.
├── Production: CPU, Memory, Network.
├── Best Practice: Used by Prometheus and Metrics Server.
├── Interview Tip: No application changes required.

---------------------------------------------------------

✅ Node Exporter
├── Concept: OS-level metrics.
├── Production: CPU, Disk, Filesystem, Load Average.
├── Best Practice: Deploy as DaemonSet.
├── Interview Tip: Node metrics, not application metrics.

---------------------------------------------------------

✅ Grafana
├── Concept: Visualizes Prometheus metrics.
├── Production: Dashboards for applications and clusters.
├── Best Practice: Build reusable dashboards.
├── Interview Tip: Grafana doesn't collect metrics.

---------------------------------------------------------

✅ Alertmanager
├── Concept: Sends alerts from Prometheus.
├── Production: Slack, Teams, Email, PagerDuty.
├── Best Practice: Reduce alert fatigue.
├── Interview Tip: Prometheus detects, Alertmanager notifies.

---------------------------------------------------------

✅ OpenTelemetry
├── Concept: Standard framework for Metrics, Logs and Traces.
├── Production: Distributed tracing.
├── Best Practice: Instrument applications once.
├── Interview Tip: Vendor-neutral observability.

---------------------------------------------------------

Quick Comparison

| Component | Purpose |
|-----------|---------|
| Logs | Application Output |
| Describe | Kubernetes Events |
| Metrics Server | CPU & Memory |
| kubectl top | Live Usage |
| Prometheus | Metrics Collection |
| Grafana | Dashboards |
| Alertmanager | Notifications |
| Node Exporter | OS Metrics |
| OpenTelemetry | Metrics + Logs + Traces |

Production Best Practices

✔ Check Events before logs.
✔ Configure Liveness & Readiness.
✔ Don't expose traffic until Readiness succeeds.
✔ Install Metrics Server.
✔ Monitor Prometheus itself.
✔ Deploy Node Exporter.
✔ Build Grafana dashboards.
✔ Alert on symptoms, not every metric.

Memory Trick

Logs
    │
Application

Describe
    │
Kubernetes

Metrics Server
    │
HPA

Prometheus
    │
Metrics

Grafana
    │
Visualization

Alertmanager
    │
Notification

Questions I Asked

Q. Why does HPA require Metrics Server?
A. Metrics Server provides CPU/Memory usage to HPA.

Q. Does Kubernetes automatically know CPU usage?
A. No. kubelet exposes metrics; Metrics Server aggregates them.

Q. Who implements /metrics?
A. Usually the application team.

Q. Does Prometheus collect system metrics automatically?
A. Yes. Through kubelet/cAdvisor and Node Exporter.

Q. What happens when Readiness fails?
A. Pod stays Running but is removed from Service endpoints.

Q. What happens when Liveness fails?
A. kubelet restarts the container.

Understanding: 100%

# Module 9 - Autoscaling ✅

We covered:

=========================================================

✅ Horizontal Pod Autoscaler (HPA)
├── Concept: Automatically increases/decreases the number of Pods.
├── Production: Used for stateless applications.
├── Best Practice: Configure CPU/Memory Requests before enabling HPA.
├── Interview Tip: HPA scales Pods, NOT Nodes.
└── Questions I Asked
    Q. Does Kubernetes automatically scale Pods?
    A. No. HPA must be configured.

    Q. Will HPA scale back down?
    A. Yes. It scales down when utilization remains below the target.

---------------------------------------------------------

✅ Metrics Server
├── Concept: Collects CPU and Memory usage from kubelets.
├── Production: Required by HPA.
├── Best Practice: Install on every cluster.
├── Interview Tip: Metrics Server stores short-term metrics only.
└── Questions I Asked
    Q. Why does HPA need Metrics Server?
    A. Kubernetes doesn't calculate resource usage itself.

---------------------------------------------------------

✅ HPA Algorithm
├── Concept: Compares current utilization against target utilization.
├── Production: Continuously reconciles desired replica count.
├── Best Practice: Set realistic target utilization.
├── Interview Tip: Uses CPU/Memory Requests as baseline.

Example

Current CPU = 90%

Target CPU = 80%

Current Pods = 3

↓

HPA scales

↓

Pods = 4~6

---------------------------------------------------------

✅ CPU Scaling
├── Concept: Scale based on average CPU utilization.
├── Production: Most common HPA metric.
├── Best Practice: CPU Requests must be defined.
├── Interview Tip: Utilization is based on Requests.

---------------------------------------------------------

✅ Memory Scaling
├── Concept: Scale based on average memory utilization.
├── Production: Useful for memory-intensive applications.
├── Best Practice: Monitor OOMKilled before enabling.
├── Interview Tip: Memory usage tends to be less elastic than CPU.

---------------------------------------------------------

✅ Custom Metrics
├── Concept: Scale using application metrics.
├── Production: Queue Length, HTTP Requests, Active Sessions.
├── Best Practice: Use Prometheus Adapter.
├── Interview Tip: Doesn't require CPU-based scaling.

---------------------------------------------------------

✅ External Metrics
├── Concept: Scale using external systems.
├── Production: Kafka, RabbitMQ, AWS SQS.
├── Best Practice: Usually implemented with KEDA.
├── Interview Tip: Doesn't depend on Pod resource usage.

---------------------------------------------------------

✅ Vertical Pod Autoscaler (VPA)
├── Concept: Adjusts CPU and Memory Requests/Limits.
├── Production: Best for long-running workloads.
├── Best Practice: Start with Recommendation Mode.
├── Interview Tip: VPA changes Pod size, not Pod count.
└── Questions I Asked
    Q. What happens after VPA changes resources?
    A. Existing Pod is recreated (depending on mode).

    Q. Doesn't this break GitOps?
    A. Recommendation Mode doesn't modify manifests.
       Platform teams review recommendations before updating Git.

---------------------------------------------------------

✅ Recommendation Mode
├── Concept: Suggests optimal CPU/Memory.
├── Production: Most commonly used mode.
├── Best Practice: Review recommendations before applying.
├── Interview Tip: Doesn't restart Pods.

---------------------------------------------------------

✅ Auto Mode
├── Concept: Automatically updates resource requests.
├── Production: Less common.
├── Best Practice: Use carefully.
├── Interview Tip: May recreate Pods.

---------------------------------------------------------

✅ Cluster Autoscaler
├── Concept: Adds or removes Worker Nodes.
├── Production: Cloud-managed node groups.
├── Best Practice: Configure min/max node counts.
├── Interview Tip: Triggered by Pending Pods.
└── Questions I Asked
    Q. Who creates the new worker node?
    A. Cluster Autoscaler talks to AWS/Azure/GCP APIs.

    Q. Does Cluster Autoscaler join the node?
    A. Cloud creates the VM, Kubernetes joins it automatically.

    Q. Does Cluster Autoscaler work locally?
    A. Usually no. Mostly designed for cloud providers.

---------------------------------------------------------

✅ Node Groups
├── Concept: Group of worker nodes managed together.
├── Production: AWS ASG, Azure VMSS, GCP MIG.
├── Best Practice: Separate workloads by node groups.
├── Interview Tip: Cluster Autoscaler scales node groups.

---------------------------------------------------------

✅ Scale Up
├── Concept: Add Worker Nodes.
├── Production: Triggered by Pending Pods.
├── Best Practice: Ensure cloud quotas allow scaling.
├── Interview Tip: Pods trigger node scaling.

---------------------------------------------------------

✅ Scale Down
├── Concept: Remove underutilized Worker Nodes.
├── Production: Reduces infrastructure cost.
├── Best Practice: Drain nodes before removal.
├── Interview Tip: Doesn't remove nodes running critical Pods.

---------------------------------------------------------

✅ KEDA
├── Concept: Event-driven autoscaling.
├── Production: Kafka, RabbitMQ, Azure Queue, SQS.
├── Best Practice: Use when CPU isn't the scaling factor.
├── Interview Tip: Creates HPA automatically.
└── Questions I Asked
    Q. Is KEDA cloud-specific?
    A. No. It works anywhere Kubernetes runs.

    Q. Does KEDA scale Worker Nodes?
    A. No. KEDA scales Pods. Cluster Autoscaler scales Nodes.

    Q. Can Prometheus scale Pods directly?
    A. No. Prometheus provides metrics. HPA performs scaling.

---------------------------------------------------------

Autoscaling Comparison

| Component | Scales |
|-----------|----------------------------------------------|
| HPA | Number of Pods |
| VPA | CPU & Memory Requests/Limits |
| Cluster Autoscaler | Worker Nodes |
| KEDA | Pods based on External Events |

---------------------------------------------------------

Production Flow

User Traffic

↓

Application CPU = 90%

↓

Metrics Server

↓

HPA

↓

Deployment

↓

Pods

↓

If Pods Pending

↓

Cluster Autoscaler

↓

Cloud Provider

↓

New Worker Node

---------------------------------------------------------

Production Best Practices

✔ Always define Requests before HPA.
✔ Start VPA in Recommendation Mode.
✔ Use Cluster Autoscaler in cloud environments.
✔ Use KEDA for event-driven workloads.
✔ Don't run HPA and VPA on CPU simultaneously.
✔ Configure PodDisruptionBudgets with Autoscaling.
✔ Monitor scaling events.

---------------------------------------------------------

Common Production Mistakes

❌ HPA without Metrics Server

❌ Missing CPU Requests

❌ VPA Auto Mode with GitOps

❌ Tiny minReplicas causing cold starts

❌ Scaling databases using HPA

---------------------------------------------------------

Memory Trick

Metrics Server
        │
        ▼
HPA
        │
Pods

──────────────

VPA
        │
Pod Size

──────────────

Pending Pods
        │
Cluster Autoscaler
        │
Worker Nodes

──────────────

Kafka
RabbitMQ
SQS
        │
KEDA
        │
HPA
        │
Pods

---------------------------------------------------------

Questions I Asked

Q. Why does HPA need Metrics Server?
A. Metrics Server provides CPU/Memory usage.

Q. Does HPA automatically scale back down?
A. Yes.

Q. What is targetCPUUtilization:80?
A. Desired average CPU utilization before scaling.

Q. Does Kubernetes automatically scale Pods?
A. No. HPA must be configured.

Q. Does VPA modify Git?
A. No. Recommendation Mode only suggests values.

Q. How do I see VPA recommendations?
A. kubectl describe vpa <name>

Q. Who installs Cluster Autoscaler?
A. Platform Team (often via Helm + FluxCD).

Q. Does Cluster Autoscaler create EC2 instances?
A. Yes. Through the cloud provider APIs.

Q. Can Cluster Autoscaler work on-prem?
A. Limited. Mostly cloud-focused.

Q. Is KEDA another HPA?
A. No. KEDA creates/manages HPA using external events.

Q. Can Prometheus scale Pods?
A. No. Prometheus exposes metrics. HPA performs scaling.

Understanding: 100%

# Module 10 - Helm / GitOps / Platform Engineering ✅

We covered:

=========================================================

✅ Helm
├── Concept: Kubernetes Package Manager.
├── Production: Standard way to deploy applications.
├── Best Practice: One reusable chart per application.
├── Interview Tip: Helm generates Kubernetes YAML.
└── Questions I Asked
    Q. Why Helm instead of plain YAML?
    A. Reuse templates across multiple environments.

---------------------------------------------------------

✅ Charts
├── Concept: Package containing Kubernetes templates.
├── Production: One chart per application/platform component.
├── Best Practice: Keep charts reusable.
├── Interview Tip: Chart = Package.

---------------------------------------------------------

✅ values.yaml
├── Concept: Stores configurable values.
├── Production: Different values for Dev/QA/Prod.
├── Best Practice: Keep templates generic.
├── Interview Tip: Templates never change; values change.

---------------------------------------------------------

✅ Templates
├── Concept: Dynamic Kubernetes manifests.
├── Production: Deployment, Service, Ingress, HPA, etc.
├── Best Practice: Parameterize everything.
├── Interview Tip: Similar to programming functions.

---------------------------------------------------------

✅ Helm Functions
├── default()
├── required()
├── quote()
├── toYaml()
├── nindent()
├── Production: Simplifies reusable templates.
├── Interview Tip: toYaml + nindent are commonly used together.

---------------------------------------------------------

✅ _helpers.tpl
├── Concept: Reusable helper functions.
├── Production: Names, Labels, ServiceAccounts.
├── Best Practice: Avoid duplicate YAML.
├── Interview Tip: include() calls helper functions.
└── Questions I Asked
    Q. Why use _helpers.tpl?
    A. Centralizes reusable template logic.

---------------------------------------------------------

✅ Chart Dependencies
├── Concept: Helm charts depending on other charts.
├── Production: Redis, PostgreSQL, Common Libraries.
├── Best Practice: Pin chart versions.
├── Interview Tip: Defined in Chart.yaml.

---------------------------------------------------------

✅ OCI Registry
├── Concept: Stores Helm Charts like container images.
├── Production: JFrog, Harbor, Azure ACR.
├── Best Practice: Version charts.
├── Interview Tip: Modern replacement for Helm repositories.

---------------------------------------------------------

✅ Helm Install
├── Concept: Deploy application.
├── Production: Usually automated through GitOps.
├── Best Practice: Avoid manual production installs.
├── Interview Tip: Creates a Helm Release.

---------------------------------------------------------

✅ Helm Upgrade
├── Concept: Update an existing release.
├── Production: Deploy new application versions.
├── Best Practice: Upgrade using GitOps.
├── Interview Tip: Supports Rolling Updates.

---------------------------------------------------------

✅ Helm Rollback
├── Concept: Restore previous release.
├── Production: Failed deployments.
├── Best Practice: Keep release history.
├── Interview Tip: Very fast recovery.

---------------------------------------------------------

✅ FluxCD
├── Concept: GitOps operator.
├── Production: Continuously reconciles Git with Kubernetes.
├── Best Practice: Never kubectl apply directly in production.
├── Interview Tip: Git becomes the Source of Truth.
└── Questions I Asked
    Q. Can Flux replace Helm?
    A. No. Flux deploys Helm charts.

---------------------------------------------------------

✅ Source Controller
├── Concept: Watches Git repositories.
├── Production: Detects Git changes.
├── Interview Tip: Downloads Git contents.

---------------------------------------------------------

✅ Helm Controller
├── Concept: Installs and upgrades Helm Releases.
├── Production: Most commonly used Flux controller.
├── Interview Tip: Executes Helm operations automatically.

---------------------------------------------------------

✅ Kustomize Controller
├── Concept: Deploys Kustomize resources.
├── Production: Platform components.
├── Interview Tip: Alternative to Helm.

---------------------------------------------------------

✅ Notification Controller
├── Concept: Sends GitOps events.
├── Production: Slack, Teams, Webhooks.
├── Interview Tip: Optional component.

---------------------------------------------------------

✅ HelmRelease
├── Concept: Kubernetes resource describing Helm deployment.
├── Production: Managed through Git.
├── Best Practice: Never edit directly in cluster.
├── Interview Tip: Flux watches HelmRelease objects.
└── Questions I Asked
    Q. HelmRelease or helm install?
    A. HelmRelease for GitOps, helm install mostly for manual deployments.

---------------------------------------------------------

✅ GitOps
├── Concept: Git is the desired state.
├── Production: Every infrastructure change goes through Git.
├── Best Practice: Pull Requests for every change.
├── Interview Tip: Git is the Single Source of Truth.
└── Questions I Asked
    Q. Does Flux continuously monitor Git?
    A. Yes.

---------------------------------------------------------

✅ GitHub
├── Concept: Stores application, Helm and GitOps repositories.
├── Production: Version control and approvals.
├── Interview Tip: Nothing changes without Git.

---------------------------------------------------------

✅ Jenkins
├── Concept: CI Pipeline.
├── Production: Build, Test, Scan, Push.
├── Best Practice: Build image only once.
├── Interview Tip: CI builds artifacts.

---------------------------------------------------------

✅ JFrog Artifactory
├── Concept: Artifact Repository.
├── Production: Docker Images and Helm Charts.
├── Best Practice: Promote same image across environments.
├── Interview Tip: Don't rebuild for QA/Prod.
└── Questions I Asked
    Q. How is image promoted to QA?
    A. Same image tag is reused. GitOps updates Helm values.

---------------------------------------------------------

✅ Rancher
├── Concept: Kubernetes Cluster Management.
├── Production: RBAC, Cluster Provisioning, Monitoring.
├── Best Practice: Manage multiple clusters centrally.
├── Interview Tip: Rancher manages clusters, not deployments.
└── Questions I Asked
    Q. Does Rancher replace Flux?
    A. No.

---------------------------------------------------------

GitOps Flow

Developer

↓

GitHub (Application Repo)

↓

Jenkins

↓

Build

↓

Security Scan

↓

JFrog (Docker Image)

↓

GitOps Repo

↓

Update Image Tag

↓

Pull Request

↓

Merge

↓

FluxCD

↓

HelmRelease

↓

Helm

↓

Kubernetes

---------------------------------------------------------

Environment Promotion

Dev

↓

Same Image

↓

QA

↓

Same Image

↓

Stage

↓

Same Image

↓

Production

Only Git changes.

No rebuild.

---------------------------------------------------------

Repository Structure

Application Repo

├── Source Code
├── Dockerfile
└── Jenkinsfile

Helm Repo

├── Chart.yaml
├── values.yaml
└── templates/

GitOps Repo

├── dev
├── qa
├── stage
└── prod

---------------------------------------------------------

Quick Comparison

| Component | Responsibility |
|----------|----------------|
| GitHub | Source Code |
| Jenkins | CI |
| JFrog | Images & Charts |
| Helm | Package Manager |
| FluxCD | GitOps |
| HelmRelease | Desired Deployment |
| Rancher | Cluster Management |

---------------------------------------------------------

Production Best Practices

✔ Build image once.
✔ Promote same image across environments.
✔ Never rebuild for Production.
✔ Git is Source of Truth.
✔ One Helm chart per application.
✔ One GitOps repo per environment (recommended).
✔ Deploy platform components through Flux.
✔ Store charts in OCI Registry.

---------------------------------------------------------

Common Production Mistakes

❌ Rebuilding images for QA/Prod.

❌ Editing Kubernetes manually.

❌ Using latest image tag.

❌ Copying Helm charts.

❌ Manual production deployments.

---------------------------------------------------------

Memory Trick

Application Repo
        │
Jenkins
        │
JFrog
        │
GitOps Repo
        │
FluxCD
        │
HelmRelease
        │
Helm
        │
Kubernetes

---------------------------------------------------------

Questions I Asked

Q. Why use Helm?
A. Reusable templates for multiple environments.

Q. Why _helpers.tpl?
A. Avoid duplicate template logic.

Q. Does Flux generate YAML?
A. No. Helm generates YAML; Flux deploys it.

Q. How does Dev become QA?
A. Update GitOps image tag. Same image is promoted.

Q. Why not rebuild for QA?
A. Same tested artifact ensures consistency.

Q. Does Rancher replace Flux?
A. No. Rancher manages clusters; Flux manages deployments.

Q. Can Velero be installed through Flux?
A. Yes. Platform components are commonly installed via HelmRelease.

Understanding: 100%


















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
