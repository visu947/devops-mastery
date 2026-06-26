# Security Labs

Welcome to the hands-on labs for the **CKA Security** chapter.

These labs are designed to teach Kubernetes security from basic workload identity to production-grade security troubleshooting.

---

# Lab Roadmap

| Lab | Topic | Difficulty |
|------|-------|------------|
| Lab 01 | Service Accounts | ⭐ |
| Lab 02 | RBAC | ⭐⭐ |
| Lab 03 | Secrets | ⭐⭐ |
| Lab 04 | Security Context | ⭐⭐⭐ |
| Lab 05 | Pod Security | ⭐⭐⭐ |
| Lab 06 | Certificates | ⭐⭐⭐⭐ |
| Lab 07 | kubeconfig | ⭐⭐⭐⭐ |
| Lab 08 | Admission Controllers | ⭐⭐⭐⭐ |
| Lab 09 | Image Security | ⭐⭐⭐⭐ |
| Lab 10 | Security Troubleshooting | ⭐⭐⭐⭐⭐ |

---

# Learning Path

```text
ServiceAccount
        │
        ▼
RBAC
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
Certificates
        │
        ▼
kubeconfig
        │
        ▼
Admission Controllers
        │
        ▼
Image Security
        │
        ▼
Production Troubleshooting
```

---

# Prerequisites

Before starting these labs, ensure you have:

- A working Kubernetes cluster.
- `kubectl` configured.
- Cluster administrator access (recommended for security labs).
- Basic understanding of Pods and YAML manifests.

---

# Expected Outcome

After completing these labs, you will be able to:

- Create and use ServiceAccounts.
- Configure RBAC permissions.
- Secure workloads using Secrets.
- Configure Pod and Container Security Contexts.
- Apply Pod Security Standards.
- Understand Kubernetes certificates and kubeconfig.
- Explain Admission Controllers.
- Troubleshoot Kubernetes security problems.

These skills are essential for the **CKA exam** and day-to-day Kubernetes administration.
