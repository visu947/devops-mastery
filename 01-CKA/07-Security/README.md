# CKA - Security

> **Goal:** Learn how Kubernetes secures clusters using Authentication, Authorization, Service Accounts, RBAC, Secrets, Security Contexts, Certificates, and Admission Controllers.

---

# 📚 Chapter Contents

* Learning Objectives
* Why Kubernetes Security Matters
* Kubernetes Security Model
* Authentication
* Authorization
* Service Accounts
* RBAC
* Secrets
* Security Context
* Pod Security
* Certificates
* Admission Controllers
* Defense in Depth
* Production Best Practices
* Summary
* References

---

# Learning Objectives

After completing this chapter, you should be able to:

* Explain the Kubernetes security model.
* Understand authentication and authorization.
* Create and manage ServiceAccounts.
* Configure RBAC Roles and RoleBindings.
* Secure applications using Secrets.
* Configure Security Contexts.
* Apply Pod Security Standards.
* Understand Kubernetes certificates.
* Explain Admission Controllers.
* Troubleshoot common Kubernetes security issues.
* Answer CKA and Senior DevOps interview questions confidently.

---

# Why Kubernetes Security Matters

A Kubernetes cluster often runs dozens or hundreds of applications.

Without proper security controls:

* Anyone could access the API server.
* Applications could access other namespaces.
* Containers could run as root.
* Secrets could be exposed.
* Privileged Pods could compromise worker nodes.

Kubernetes secures clusters through multiple layers rather than relying on a single mechanism.

---

# Kubernetes Security Model

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

Every API request follows this sequence.

---

# Authentication

Authentication answers:

> **Who are you?**

Kubernetes supports multiple authentication methods, including:

* Client certificates
* ServiceAccounts
* OpenID Connect (OIDC)
* Authentication tokens
* External identity providers

If authentication fails, the request is rejected immediately.

---

# Authorization

Authorization answers:

> **What are you allowed to do?**

After a user is authenticated, Kubernetes evaluates permissions.

Common authorization modes include:

* RBAC (most common)
* Node Authorization
* Webhook Authorization

---

# Service Accounts

Applications running inside Kubernetes Pods authenticate using ServiceAccounts.

A ServiceAccount provides an identity for workloads running inside the cluster.

Typical use cases:

* Pods calling the Kubernetes API
* Controllers
* Operators
* CI/CD systems

---

# RBAC

Role-Based Access Control (RBAC) defines what authenticated users and ServiceAccounts can do.

Core resources:

* Role
* ClusterRole
* RoleBinding
* ClusterRoleBinding

RBAC follows the principle of least privilege.

---

# Secrets

Secrets securely store sensitive information.

Examples:

* Passwords
* API Keys
* TLS Certificates
* Tokens

Applications access Secrets through:

* Environment variables
* Mounted volumes

---

# Security Context

A Security Context defines security settings for a Pod or Container.

Examples:

* Run as non-root
* User ID (UID)
* Group ID (GID)
* Read-only root filesystem
* Linux capabilities
* Privilege escalation

---

# Pod Security

Pod Security Standards help prevent insecure workloads.

Profiles:

* Privileged
* Baseline
* Restricted

These profiles reduce the attack surface of Kubernetes workloads.

---

# Certificates

Kubernetes uses TLS certificates for secure communication.

Certificates secure:

* API Server
* kubelet
* Controller Manager
* Scheduler
* etcd
* kubectl clients

All communication between Kubernetes components should be encrypted.

---

# Admission Controllers

Admission Controllers intercept requests after authentication and authorization but before objects are persisted.

They can:

* Validate requests
* Mutate objects
* Enforce policies

Examples:

* NamespaceLifecycle
* LimitRanger
* ResourceQuota
* DefaultStorageClass
* PodSecurity Admission

---

# Defense in Depth

Kubernetes security is based on multiple independent layers.

```text
Internet
    │
    ▼
API Server
    │
    ▼
Authentication
    │
    ▼
Authorization (RBAC)
    │
    ▼
Admission Controllers
    │
    ▼
Pod Security
    │
    ▼
Container Security
    │
    ▼
Worker Node
```

If one layer fails, additional layers continue protecting the cluster.

---

# Production Best Practices

* Enable RBAC.
* Follow the Principle of Least Privilege.
* Use dedicated ServiceAccounts.
* Avoid running containers as root.
* Store sensitive data in Secrets.
* Enable Pod Security Standards.
* Rotate certificates regularly.
* Keep Kubernetes components up to date.
* Audit API activity.
* Restrict privileged containers.

---

# Security Workflow

```text
User

↓

Authenticate

↓

Authorize

↓

Admission Controller

↓

API Server

↓

Persist Object

↓

Scheduler

↓

kubelet

↓

Running Pod
```

---

# Summary

In this chapter you learned:

* Kubernetes security architecture.
* Authentication.
* Authorization.
* ServiceAccounts.
* RBAC.
* Secrets.
* Security Contexts.
* Pod Security.
* Certificates.
* Admission Controllers.
* Production security best practices.

---

# References

* Kubernetes Authentication Documentation
* Kubernetes Authorization Documentation
* RBAC Documentation
* Secrets Documentation
* Security Context Documentation
* Pod Security Standards
* Certificates Documentation
* Admission Controllers Documentation
* CKA Curriculum
