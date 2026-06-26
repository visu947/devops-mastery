# Security - Interview Questions & Answers

> **Frequently asked Kubernetes Security interview questions for the CKA exam and Senior DevOps interviews.**

---

# Beginner Level

## 1. What is Kubernetes security?

### Answer

Kubernetes security is a layered approach that protects:

* The Kubernetes API
* Cluster resources
* Workloads
* Sensitive data
* Worker nodes

It includes:

* Authentication
* Authorization
* Admission Controllers
* RBAC
* ServiceAccounts
* Secrets
* Security Contexts
* Pod Security Standards

---

## 2. What is Authentication?

### Answer

Authentication verifies the identity of a user or application.

It answers:

> **Who are you?**

Common authentication methods:

* Client certificates
* ServiceAccounts
* Bearer tokens
* OpenID Connect (OIDC)
* External identity providers

---

## 3. What is Authorization?

### Answer

Authorization determines what an authenticated identity is allowed to do.

It answers:

> **What are you allowed to do?**

Kubernetes commonly uses:

* RBAC
* Node Authorization
* Webhook Authorization

---

## 4. What is the difference between Authentication and Authorization?

### Answer

| Authentication    | Authorization                |
| ----------------- | ---------------------------- |
| Verifies identity | Verifies permissions         |
| Happens first     | Happens after authentication |
| "Who are you?"    | "What can you do?"           |

---

## 5. What is a ServiceAccount?

### Answer

A ServiceAccount provides an identity for Pods running inside Kubernetes.

It allows applications to authenticate to the Kubernetes API.

Typical use cases:

* Operators
* Controllers
* CI/CD pipelines
* Applications interacting with the cluster

---

## 6. What is RBAC?

### Answer

RBAC (Role-Based Access Control) controls access to Kubernetes resources.

It grants permissions based on Roles and Bindings instead of assigning permissions directly to users.

---

## 7. What is a Secret?

### Answer

A Secret stores sensitive information such as:

* Passwords
* API keys
* TLS certificates
* Authentication tokens

Applications can consume Secrets through environment variables or mounted volumes.

---

# Intermediate Level

## 8. Explain the RBAC resources.

### Answer

```text id="n5j24t"
User / ServiceAccount
          │
          ▼
Role / ClusterRole
          │
          ▼
RoleBinding / ClusterRoleBinding
          │
          ▼
Permissions
```

* **Role** → Namespace-scoped permissions.
* **ClusterRole** → Cluster-wide permissions.
* **RoleBinding** → Grants a Role or ClusterRole within a namespace.
* **ClusterRoleBinding** → Grants cluster-wide permissions.

---

## 9. Role vs ClusterRole?

### Answer

| Role                            | ClusterRole                  |
| ------------------------------- | ---------------------------- |
| Namespace scoped                | Cluster scoped               |
| Used within one namespace       | Can apply across the cluster |
| Cannot manage cluster resources | Can manage cluster resources |

---

## 10. RoleBinding vs ClusterRoleBinding?

### Answer

* **RoleBinding** grants permissions within a namespace.
* **ClusterRoleBinding** grants permissions across the entire cluster.

---

## 11. What is a Security Context?

### Answer

A Security Context defines runtime security settings for a Pod or container.

Examples:

* `runAsUser`
* `runAsNonRoot`
* `fsGroup`
* `readOnlyRootFilesystem`
* `allowPrivilegeEscalation`

It helps reduce the attack surface of workloads.

---

## 12. What are Pod Security Standards?

### Answer

Pod Security Standards define security levels for Pods.

Profiles:

* Privileged
* Baseline
* Restricted

The **Restricted** profile is recommended for most production workloads.

---

## 13. What are Admission Controllers?

### Answer

Admission Controllers intercept API requests after authentication and authorization but before objects are stored.

They can:

* Validate objects.
* Modify objects.
* Enforce cluster policies.

---

## 14. Why are Secrets Base64 encoded?

### Answer

Base64 is **encoding**, not encryption.

It makes binary data safe to store in YAML or JSON but does **not** provide confidentiality.

To protect Secrets:

* Enable etcd encryption at rest.
* Restrict RBAC access.
* Consider external secret management solutions (such as cloud secret managers or HashiCorp Vault).

---

# Advanced Level

## 15. Explain the Kubernetes security workflow.

### Answer

```text id="65jlwm"
User / Application

↓

Authentication

↓

Authorization

↓

Admission Controllers

↓

API Server

↓

etcd
```

Every API request follows this sequence.

---

## 16. How do Pods authenticate to the Kubernetes API?

### Answer

Pods authenticate using their assigned ServiceAccount.

Kubernetes mounts a ServiceAccount token into the Pod, which is used when calling the API server (unless token automounting is disabled).

---

## 17. How would you verify permissions?

### Answer

Use:

```bash id="e19slj"
kubectl auth can-i get pods

kubectl auth can-i create deployments

kubectl auth can-i "*" "*"
```

This is one of the fastest ways to troubleshoot RBAC issues.

---

## 18. What happens when RBAC denies access?

### Answer

The API server returns a **Forbidden** response.

Example:

```text id="9n3hxh"
Error from server (Forbidden)
```

The user is authenticated but lacks the required permissions.

---

## 19. Why should containers avoid running as root?

### Answer

Running as root increases the impact of a container compromise.

Production workloads should:

* Run as a non-root user.
* Drop unnecessary Linux capabilities.
* Prevent privilege escalation.
* Use a read-only root filesystem when practical.

---

## 20. How would you secure a production cluster?

### Answer

A layered approach includes:

* RBAC
* Dedicated ServiceAccounts
* Secrets
* Security Contexts
* Pod Security Standards
* Network Policies
* Image scanning
* Certificate rotation
* Audit logging
* Regular Kubernetes updates

---

# Production Scenarios

## 21. A Pod receives a "Forbidden" error when calling the API. How do you troubleshoot it?

### Answer

1. Identify the ServiceAccount used by the Pod.
2. Verify the assigned Role or ClusterRole.
3. Verify the RoleBinding or ClusterRoleBinding.
4. Test permissions with:

```bash id="hkf2xg"
kubectl auth can-i <verb> <resource> --as=<user-or-serviceaccount>
```

5. Grant only the minimum permissions required.

---

## 22. A Secret exists, but the Pod cannot read it. Why?

### Answer

Possible causes:

* Secret is in a different namespace.
* Incorrect Secret name.
* Volume or environment variable misconfiguration.
* Pod was not updated after the Secret changed (depending on how it is consumed).

---

## 23. A Pod is rejected during creation. What do you check?

### Answer

Review:

* Pod Security labels on the namespace.
* Security Context settings.
* Admission Controller policies.
* Cluster events.

---

## 24. How do you audit RBAC permissions?

### Answer

Review:

```bash id="tjlwmz"
kubectl get roles

kubectl get rolebindings

kubectl get clusterroles

kubectl get clusterrolebindings

kubectl auth can-i
```

Look for permissions that are broader than necessary.

---

## 25. Why should applications use dedicated ServiceAccounts?

### Answer

Benefits:

* Least privilege.
* Better auditing.
* Easier troubleshooting.
* Reduced blast radius if credentials are compromised.

Avoid sharing the default ServiceAccount across unrelated workloads.

---

# Rapid Fire

### Authentication happens before Authorization?

**Yes.**

---

### Which resource grants namespace permissions?

**RoleBinding**

---

### Which resource grants cluster-wide permissions?

**ClusterRoleBinding**

---

### What command verifies permissions?

```bash id="2ht70n"
kubectl auth can-i
```

---

### Are Secrets encrypted by default?

No. They are Base64 encoded. Encryption at rest must be configured separately.

---

### What identity does a Pod use?

Its assigned **ServiceAccount**.

---

### What is the most secure Pod Security profile?

**Restricted**

---

### Where are Kubernetes objects stored?

**etcd**

---

# Interview Tips

When answering Kubernetes security questions:

* Start with the security workflow.
* Explain the specific component.
* Mention where it fits into the request lifecycle.
* Include least privilege as a best practice.
* Describe how you would troubleshoot or verify the configuration.

This approach demonstrates both conceptual understanding and operational experience.
