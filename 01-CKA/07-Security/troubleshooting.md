# Security - Troubleshooting Guide

> **Systematic approach to troubleshooting Kubernetes security issues in production and the CKA exam.**

---

# Security Troubleshooting Workflow

```text
Security Error
      │
      ▼
Authentication
      │
      ▼
Authorization (RBAC)
      │
      ▼
ServiceAccount
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
Certificates / kubeconfig
      │
      ▼
Root Cause
```

---

# Scenario 1 - Forbidden Error

## Symptoms

```text
Error from server (Forbidden)
```

Example:

```text
Error from server (Forbidden):
pods is forbidden:
User "system:serviceaccount:default:default"
cannot list resource "pods"
```

---

## Investigation

```bash
kubectl auth can-i get pods

kubectl describe role

kubectl describe rolebinding

kubectl describe clusterrole

kubectl describe clusterrolebinding
```

---

## Common Causes

* Missing Role
* Missing RoleBinding
* Wrong namespace
* Incorrect ServiceAccount

---

## Resolution

Verify:

* Role
* RoleBinding
* Namespace
* ServiceAccount
* `kubectl auth can-i`

---

# Scenario 2 - Unauthorized Error

## Symptoms

```text
Unauthorized
```

---

## Investigation

```bash
kubectl config current-context

kubectl config view

kubectl cluster-info
```

---

## Common Causes

* Invalid credentials
* Expired certificate
* Invalid token
* Incorrect kubeconfig

---

## Resolution

* Verify kubeconfig.
* Verify credentials.
* Verify certificates.
* Verify API server connectivity.

---

# Scenario 3 - Pod Cannot Access API Server

## Symptoms

Application receives:

```text
403 Forbidden

or

401 Unauthorized
```

---

## Investigation

Verify the ServiceAccount:

```bash
kubectl get sa

kubectl describe sa <serviceaccount-name>
```

Check the Pod:

```bash
kubectl describe pod <pod-name>
```

---

## Resolution

* Ensure the correct ServiceAccount is assigned.
* Verify RBAC permissions.
* Test with:

```bash
kubectl auth can-i <verb> <resource> \
--as=system:serviceaccount:<namespace>:<serviceaccount>
```

---

# Scenario 4 - Secret Not Found

## Symptoms

```text
secret "<name>" not found
```

---

## Investigation

```bash
kubectl get secrets

kubectl describe secret <secret-name>

kubectl get pod <pod-name> -o yaml
```

---

## Common Causes

* Wrong Secret name.
* Secret in another namespace.
* Typo in Pod manifest.

---

## Resolution

Verify:

* Secret exists.
* Correct namespace.
* Correct reference in the Pod spec.

---

# Scenario 5 - Secret Value Incorrect

## Investigation

```bash
kubectl get secret <secret-name> \
-o yaml
```

Decode a value:

```bash
kubectl get secret <secret-name> \
-o jsonpath='{.data.password}' | base64 --decode
```

---

## Resolution

* Confirm the expected key exists.
* Verify the decoded value.
* Recreate or update the Secret if necessary.

---

# Scenario 6 - Pod Rejected by Pod Security

## Symptoms

```text
Error creating:

violates PodSecurity
```

---

## Investigation

```bash
kubectl get ns --show-labels

kubectl describe pod <pod-name>

kubectl get events --sort-by=.lastTimestamp
```

---

## Common Causes

* Privileged container
* Running as root
* Host networking
* HostPath usage
* Missing Security Context

---

## Resolution

Review:

* Namespace Pod Security labels.
* Pod Security Context.
* Container configuration.

---

# Scenario 7 - Container Running as Root

## Investigation

Inside the Pod:

```bash
kubectl exec -it <pod-name> -- id
```

---

## Resolution

Configure:

```yaml
securityContext:
  runAsNonRoot: true
  runAsUser: 1000
```

---

# Scenario 8 - RBAC Permission Issues

## Investigation

```bash
kubectl auth can-i get pods

kubectl auth can-i create deployments

kubectl auth can-i "*" "*"
```

Test another identity:

```bash
kubectl auth can-i list pods \
--as=<username>
```

---

## Resolution

Grant only the required permissions.

Avoid using `cluster-admin` unless absolutely necessary.

---

# Scenario 9 - Certificate Problems

## Symptoms

```text
certificate signed by unknown authority

or

x509 certificate error
```

---

## Investigation

```bash
kubectl config view

kubectl config current-context

kubectl cluster-info
```

---

## Common Causes

* Expired certificate
* Wrong certificate authority
* Incorrect kubeconfig

---

## Resolution

* Verify kubeconfig.
* Verify cluster certificates.
* Ensure the client trusts the cluster CA.

---

# Scenario 10 - Admission Controller Rejection

## Symptoms

```text
admission webhook denied the request
```

or

```text
validation failed
```

---

## Investigation

```bash
kubectl describe pod <pod-name>

kubectl get events --sort-by=.lastTimestamp
```

---

## Resolution

Determine which admission policy rejected the request and update the manifest to satisfy the policy rather than bypassing it.

---

# Common Troubleshooting Commands

```bash
kubectl auth can-i get pods

kubectl get sa

kubectl describe sa <serviceaccount-name>

kubectl get roles

kubectl get rolebindings

kubectl get clusterroles

kubectl get clusterrolebindings

kubectl get secrets

kubectl describe secret <secret-name>

kubectl get ns --show-labels

kubectl config current-context

kubectl config view

kubectl cluster-info

kubectl describe pod <pod-name>

kubectl get events --sort-by=.lastTimestamp
```

---

# Security Troubleshooting Checklist

When investigating a security problem, verify:

* Authentication
* Authorization (RBAC)
* ServiceAccount
* Namespace
* Secrets
* Security Context
* Pod Security labels
* Admission Controllers
* kubeconfig
* Certificates

Avoid making changes until you've identified the root cause.

---

# Common Security Errors

| Error                    | Likely Cause                          |
| ------------------------ | ------------------------------------- |
| Forbidden                | RBAC permissions missing              |
| Unauthorized             | Authentication failure                |
| Secret not found         | Wrong name or namespace               |
| FailedMount (Secret)     | Secret missing or incorrect reference |
| PodSecurity violation    | Pod does not meet security policy     |
| x509 certificate error   | Certificate or CA issue               |
| admission webhook denied | Admission policy rejected the request |

---

# Production Tips

* Start with the exact error message.
* Use `kubectl auth can-i` before changing RBAC.
* Verify the ServiceAccount used by the Pod.
* Keep ServiceAccounts dedicated to individual workloads.
* Check namespace labels when Pods are rejected.
* Decode Secret values only when troubleshooting and handle them carefully.
* Review events after any failed Pod creation.

---

# CKA Exam Tips

If a workload cannot access the Kubernetes API:

1. Check the ServiceAccount.
2. Check RBAC.
3. Run `kubectl auth can-i`.
4. Verify the namespace.
5. Inspect Pod events.

If a Pod is rejected:

1. Read the error message.
2. Check namespace Pod Security labels.
3. Review the Security Context.
4. Inspect events.
5. Correct the manifest instead of disabling security controls.

Following a consistent troubleshooting process is faster and more reliable than guessing or repeatedly editing YAML files.
