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
