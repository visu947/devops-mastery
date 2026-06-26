# Security - Common Mistakes

> **Learn the most common Kubernetes security mistakes, why they happen, and how to avoid them in production.**

---

# 1. Using the Default ServiceAccount

## Mistake

Deploying applications without specifying a dedicated ServiceAccount.

## Why It Happens

The default ServiceAccount is automatically assigned to Pods, so it's easy to overlook.

## Problem

* Multiple applications share the same identity.
* Permissions become difficult to audit.
* Increased security risk if one workload is compromised.

## Correct Approach

Create a dedicated ServiceAccount for each application that needs Kubernetes API access.

---

# 2. Granting Excessive RBAC Permissions

## Mistake

Granting cluster-admin or wildcard permissions unnecessarily.

Example:

```yaml id="s1q7jn"
rules:
- apiGroups: ["*"]
  resources: ["*"]
  verbs: ["*"]
```

## Why It Happens

It is quick and avoids permission errors during development.

## Problem

Violates the Principle of Least Privilege.

## Correct Approach

Grant only the permissions required for the workload.

---

# 3. Confusing Role and ClusterRole

## Mistake

Using a ClusterRole when a namespace-scoped Role is sufficient.

## Problem

Applications receive broader access than necessary.

## Correct Approach

* Use **Role** for namespace-specific permissions.
* Use **ClusterRole** only when cluster-wide access is required.

---

# 4. Storing Sensitive Data in ConfigMaps

## Mistake

Saving passwords, tokens, or API keys in ConfigMaps.

## Problem

ConfigMaps are intended for non-sensitive configuration.

## Correct Approach

Store sensitive data in Secrets and restrict access through RBAC.

---

# 5. Assuming Secrets Are Encrypted

## Mistake

Believing Kubernetes Secrets are encrypted by default.

## Reality

Secrets are Base64 encoded, not encrypted.

## Correct Approach

* Enable etcd encryption at rest.
* Restrict Secret access using RBAC.
* Consider external secret management solutions when appropriate.

---

# 6. Running Containers as Root

## Mistake

Allowing containers to run as the root user.

## Problem

A compromised container has greater privileges, increasing the impact of an attack.

## Correct Approach

Use a Security Context:

```yaml id="9k3vsz"
securityContext:
  runAsNonRoot: true
```

---

# 7. Allowing Privilege Escalation

## Mistake

Leaving privilege escalation enabled.

## Problem

Processes inside the container may gain additional privileges.

## Correct Approach

```yaml id="ej6vqy"
securityContext:
  allowPrivilegeEscalation: false
```

---

# 8. Ignoring Pod Security Standards

## Mistake

Not enforcing Pod Security Standards.

## Problem

Pods may:

* Run as root.
* Use privileged mode.
* Mount sensitive host paths.

## Correct Approach

Apply the **Restricted** profile for production workloads whenever possible.

---

# 9. Sharing ServiceAccounts Across Applications

## Mistake

Using the same ServiceAccount for unrelated workloads.

## Problem

Compromising one application may expose permissions intended for another.

## Correct Approach

Assign a dedicated ServiceAccount to each application.

---

# 10. Ignoring `kubectl auth can-i`

## Mistake

Changing RBAC resources without verifying permissions.

## Correct Approach

Use:

```bash id="g4k2ur"
kubectl auth can-i get pods

kubectl auth can-i create deployments
```

This quickly confirms effective permissions.

---

# 11. Forgetting Namespace Boundaries

## Mistake

Assuming Roles or Secrets are available across namespaces.

## Problem

* Roles are namespace-scoped.
* RoleBindings are namespace-scoped.
* Secrets are namespace-scoped.

## Correct Approach

Verify that resources exist in the correct namespace.

---

# 12. Skipping Security Reviews

## Mistake

Creating RBAC rules and never reviewing them.

## Problem

Permissions often accumulate over time.

## Correct Approach

Regularly audit:

* Roles
* RoleBindings
* ClusterRoles
* ClusterRoleBindings
* ServiceAccounts

Remove permissions that are no longer needed.

---

# Common Security Mistakes

| Mistake                       | Better Practice                  |
| ----------------------------- | -------------------------------- |
| Use default ServiceAccount    | Create dedicated ServiceAccounts |
| Grant cluster-admin           | Follow Least Privilege           |
| Use ClusterRole unnecessarily | Prefer Role where possible       |
| Store secrets in ConfigMaps   | Use Secrets                      |
| Assume Secrets are encrypted  | Enable encryption at rest        |
| Run containers as root        | Use `runAsNonRoot`               |
| Allow privilege escalation    | Disable it unless required       |
| Ignore Pod Security           | Enforce Pod Security Standards   |
| Share ServiceAccounts         | Use one per application          |
| Skip RBAC validation          | Use `kubectl auth can-i`         |

---

# Production Best Practices

* Use dedicated ServiceAccounts.
* Follow the Principle of Least Privilege.
* Review RBAC permissions regularly.
* Store credentials in Secrets.
* Enable encryption at rest for Secrets.
* Run containers as non-root.
* Apply Pod Security Standards.
* Rotate certificates and credentials.
* Enable audit logging.
* Keep Kubernetes components up to date.

---

# Interview Perspective

A common interview question is:

> **"What are the biggest Kubernetes security mistakes teams make?"**

A strong answer includes:

1. Using the default ServiceAccount.
2. Granting excessive RBAC permissions.
3. Running containers as root.
4. Storing secrets in ConfigMaps.
5. Assuming Secrets are encrypted.
6. Ignoring Pod Security Standards.
7. Sharing ServiceAccounts across workloads.
8. Never auditing RBAC permissions.

These issues are responsible for many real-world Kubernetes security weaknesses and are often straightforward to prevent with good operational practices.
