# Security - Cheat Sheet

> **Quick revision guide for Kubernetes Security concepts, commands, and interview tips.**

---

# Kubernetes Security Flow

```text
User / Application
        │
        ▼
Authentication
        │
        ▼
Authorization
        │
        ▼
Admission Controllers
        │
        ▼
API Server
        │
        ▼
etcd
```

Remember:

* Authentication = Who are you?
* Authorization = What can you do?
* Admission = Should this request be allowed?

---

# Authentication vs Authorization

| Authentication                  | Authorization                |
| ------------------------------- | ---------------------------- |
| Verifies identity               | Verifies permissions         |
| Who are you?                    | What are you allowed to do?  |
| Happens first                   | Happens after authentication |
| Uses certificates, tokens, OIDC | Uses RBAC, Node, Webhook     |

---

# RBAC Components

```text
User / ServiceAccount
          │
          ▼
Role / ClusterRole
          │
          ▼
RoleBinding / ClusterRoleBinding
          │
          ▼
Permissions Granted
```

---

# RBAC Resources

| Resource           | Scope     |
| ------------------ | --------- |
| Role               | Namespace |
| ClusterRole        | Cluster   |
| RoleBinding        | Namespace |
| ClusterRoleBinding | Cluster   |

---

# ServiceAccount Workflow

```text
Pod
 │
 ▼
ServiceAccount
 │
 ▼
Authentication Token
 │
 ▼
API Server
 │
 ▼
RBAC Authorization
```

---

# Secrets

Common Secret types:

| Type                                | Purpose              |
| ----------------------------------- | -------------------- |
| Opaque                              | Generic secrets      |
| kubernetes.io/tls                   | TLS certificates     |
| kubernetes.io/dockerconfigjson      | Registry credentials |
| kubernetes.io/service-account-token | ServiceAccount token |

---

# Security Context

Common settings:

| Setting                  | Purpose                            |
| ------------------------ | ---------------------------------- |
| runAsUser                | Run as a specific UID              |
| runAsGroup               | Run as a specific GID              |
| runAsNonRoot             | Prevent running as root            |
| fsGroup                  | Shared filesystem group            |
| readOnlyRootFilesystem   | Read-only container filesystem     |
| allowPrivilegeEscalation | Allow or deny privilege escalation |

---

# Pod Security Standards

| Profile    | Description                             |
| ---------- | --------------------------------------- |
| Privileged | Minimal restrictions                    |
| Baseline   | Prevents common privilege escalation    |
| Restricted | Most secure, recommended for production |

---

# Certificates

Used to secure communication between:

* kubectl ↔ API Server
* kubelet ↔ API Server
* Controller Manager ↔ API Server
* Scheduler ↔ API Server
* API Server ↔ etcd

---

# Admission Controllers

Request Flow:

```text
Authenticate
      │
      ▼
Authorize
      │
      ▼
Admission Controller
      │
      ▼
Persist Object
```

Examples:

* NamespaceLifecycle
* ResourceQuota
* LimitRanger
* DefaultStorageClass
* PodSecurity Admission

---

# Common Commands

```bash
kubectl get sa

kubectl get roles

kubectl get rolebindings

kubectl get clusterroles

kubectl get clusterrolebindings

kubectl get secrets

kubectl auth can-i get pods

kubectl describe pod <pod-name>

kubectl config view

kubectl get ns --show-labels
```

---

# Security Decision Tree

```text
Need Pod identity?
        │
        ▼
ServiceAccount

Need user permissions?
        │
        ▼
RBAC

Need sensitive data?
        │
        ▼
Secret

Need container restrictions?
        │
        ▼
Security Context

Need namespace-wide policy?
        │
        ▼
Pod Security

Need request validation?
        │
        ▼
Admission Controller
```

---

# Common Problems

| Problem                | Likely Cause                     |
| ---------------------- | -------------------------------- |
| Forbidden              | RBAC permission missing          |
| Unauthorized           | Authentication failed            |
| Secret not found       | Wrong namespace or name          |
| Pod rejected           | Pod Security or Admission policy |
| API access denied      | ServiceAccount lacks permissions |
| Container runs as root | Missing Security Context         |

---

# Troubleshooting Workflow

```text
Request Failed
      │
      ▼
Authentication
      │
      ▼
Authorization
      │
      ▼
ServiceAccount
      │
      ▼
RBAC
      │
      ▼
Pod Security
      │
      ▼
Admission Controller
      │
      ▼
Root Cause
```

---

# Production Best Practices

* Enable RBAC.
* Follow the Principle of Least Privilege.
* Use dedicated ServiceAccounts.
* Avoid running containers as root.
* Store credentials in Secrets.
* Apply Pod Security Standards.
* Rotate certificates regularly.
* Audit API access.
* Restrict privileged Pods.

---

# Interview Tips

### Authentication vs Authorization

* Authentication verifies identity.
* Authorization verifies permissions.

---

### Role vs ClusterRole

* Role is namespace-scoped.
* ClusterRole is cluster-scoped.

---

### RoleBinding vs ClusterRoleBinding

* RoleBinding grants permissions in a namespace.
* ClusterRoleBinding grants cluster-wide permissions.

---

### Why use ServiceAccounts?

To provide an identity for Pods that need to communicate with the Kubernetes API.

---

### Why use Secrets?

To store sensitive information separately from application code and configuration.

---

### Why use Security Context?

To enforce secure runtime settings such as running containers as a non-root user.

---

# CKA Memory Map

```text
Authentication
       │
       ▼
Authorization
       │
       ▼
RBAC
       │
       ▼
ServiceAccounts
       │
       ▼
Secrets
       │
       ▼
Security Context
       │
       ▼
Pod Security
       │
       ▼
Admission Controllers
       │
       ▼
Secure Kubernetes Cluster
```
