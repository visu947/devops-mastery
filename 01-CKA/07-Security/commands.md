# Security - Commands

> **Quick reference for Kubernetes Security commands used in the CKA exam, production environments, and technical interviews.**

---

# Table of Contents

1. Service Accounts
2. RBAC
3. Secrets
4. Security Context
5. Pod Security
6. Certificates
7. Authentication & Authorization
8. Troubleshooting
9. Useful Output Formats
10. Explain Resources

---

# 1. Service Accounts

## List Service Accounts

```bash
kubectl get serviceaccounts

kubectl get sa
```

---

## List Service Accounts in All Namespaces

```bash
kubectl get sa -A
```

---

## Describe a Service Account

```bash
kubectl describe sa <serviceaccount-name>
```

---

## Create a Service Account

```bash
kubectl create serviceaccount <serviceaccount-name>
```

---

## Delete a Service Account

```bash
kubectl delete sa <serviceaccount-name>
```

---

# 2. RBAC

## List Roles

```bash
kubectl get roles
```

---

## List ClusterRoles

```bash
kubectl get clusterroles
```

---

## List RoleBindings

```bash
kubectl get rolebindings
```

---

## List ClusterRoleBindings

```bash
kubectl get clusterrolebindings
```

---

## Describe a Role

```bash
kubectl describe role <role-name>
```

---

## Describe a ClusterRole

```bash
kubectl describe clusterrole <clusterrole-name>
```

---

## Describe a RoleBinding

```bash
kubectl describe rolebinding <rolebinding-name>
```

---

## Describe a ClusterRoleBinding

```bash
kubectl describe clusterrolebinding <binding-name>
```

---

## Check User Permissions

```bash
kubectl auth can-i get pods

kubectl auth can-i create deployments

kubectl auth can-i "*" "*"
```

---

## Check Another User's Permissions

```bash
kubectl auth can-i get pods \
--as=<username>
```

---

## Check Permissions in a Namespace

```bash
kubectl auth can-i create pods \
--namespace=<namespace>
```

---

# 3. Secrets

## List Secrets

```bash
kubectl get secrets
```

---

## Describe a Secret

```bash
kubectl describe secret <secret-name>
```

---

## View Secret YAML

```bash
kubectl get secret <secret-name> -o yaml
```

---

## Decode Secret Value

```bash
kubectl get secret <secret-name> \
-o jsonpath='{.data.password}' | base64 --decode
```

---

## Create Generic Secret

```bash
kubectl create secret generic my-secret \
--from-literal=username=admin \
--from-literal=password=secret123
```

---

## Create Secret From File

```bash
kubectl create secret generic app-secret \
--from-file=config.txt
```

---

## Create TLS Secret

```bash
kubectl create secret tls tls-secret \
--cert=server.crt \
--key=server.key
```

---

# 4. Security Context

## View Pod Security Context

```bash
kubectl get pod <pod-name> -o yaml
```

---

## Describe Pod

```bash
kubectl describe pod <pod-name>
```

---

## Verify Running User

```bash
kubectl exec -it <pod-name> -- id
```

---

## Verify Mounted Files

```bash
kubectl exec -it <pod-name> -- ls -l
```

---

# 5. Pod Security

## View Namespace Labels

```bash
kubectl get ns --show-labels
```

---

## Label Namespace with Pod Security Level

Restricted:

```bash
kubectl label namespace default \
pod-security.kubernetes.io/enforce=restricted
```

Baseline:

```bash
kubectl label namespace default \
pod-security.kubernetes.io/enforce=baseline
```

Privileged:

```bash
kubectl label namespace default \
pod-security.kubernetes.io/enforce=privileged
```

---

# 6. Certificates

## View kubeconfig

```bash
kubectl config view
```

---

## View Current Context

```bash
kubectl config current-context
```

---

## View Available Contexts

```bash
kubectl config get-contexts
```

---

## Switch Context

```bash
kubectl config use-context <context-name>
```

---

## View Cluster Information

```bash
kubectl cluster-info
```

---

# 7. Authentication & Authorization

## View Current User

```bash
kubectl config view --minify
```

---

## Verify API Access

```bash
kubectl auth can-i get pods
```

---

## Verify Namespace Permissions

```bash
kubectl auth can-i list pods \
-n kube-system
```

---

# 8. Troubleshooting

## View Events

```bash
kubectl get events --sort-by=.lastTimestamp
```

---

## Describe Pod

```bash
kubectl describe pod <pod-name>
```

---

## View Pod Logs

```bash
kubectl logs <pod-name>
```

---

## Verify Service Account

```bash
kubectl describe sa
```

---

## Verify RBAC

```bash
kubectl auth can-i list pods
```

---

## View Namespace Labels

```bash
kubectl get ns --show-labels
```

---

# 9. Useful Output Formats

## Service Account YAML

```bash
kubectl get sa <serviceaccount-name> -o yaml
```

---

## Role YAML

```bash
kubectl get role <role-name> -o yaml
```

---

## ClusterRole YAML

```bash
kubectl get clusterrole <role-name> -o yaml
```

---

## Secret YAML

```bash
kubectl get secret <secret-name> -o yaml
```

---

## Pod YAML

```bash
kubectl get pod <pod-name> -o yaml
```

---

# 10. Explain Resources

## ServiceAccount

```bash
kubectl explain serviceaccount
```

---

## Role

```bash
kubectl explain role
```

---

## RoleBinding

```bash
kubectl explain rolebinding
```

---

## ClusterRole

```bash
kubectl explain clusterrole
```

---

## ClusterRoleBinding

```bash
kubectl explain clusterrolebinding
```

---

## Secret

```bash
kubectl explain secret
```

---

## Pod Security Context

```bash
kubectl explain pod.spec.securityContext
```

---

## Container Security Context

```bash
kubectl explain pod.spec.containers.securityContext
```

---

# Most Common Security Troubleshooting Commands

```bash
kubectl get sa

kubectl get roles

kubectl get rolebindings

kubectl get clusterroles

kubectl get clusterrolebindings

kubectl get secrets

kubectl auth can-i get pods

kubectl describe pod <pod-name>

kubectl describe sa <serviceaccount-name>

kubectl get ns --show-labels

kubectl get events --sort-by=.lastTimestamp
```

---

# Production Tips

* Use dedicated ServiceAccounts for workloads.
* Grant the minimum RBAC permissions required.
* Store sensitive information in Secrets instead of ConfigMaps.
* Run containers as non-root whenever possible.
* Apply Pod Security Standards at the namespace level.
* Regularly review Roles and RoleBindings.
* Verify permissions using `kubectl auth can-i` before making RBAC changes.

---

# CKA Exam Tips

✔ Permission denied?

Check:

* ServiceAccount
* Role or ClusterRole
* RoleBinding or ClusterRoleBinding
* Namespace
* `kubectl auth can-i`

✔ Pod cannot access Kubernetes API?

Check:

* ServiceAccount assignment
* RBAC permissions
* Mounted ServiceAccount token

✔ Pod rejected during creation?

Check:

* Pod Security labels
* Security Context
* Admission policies

---

# References

* Kubernetes Authentication Documentation
* Kubernetes Authorization Documentation
* RBAC Documentation
* Secrets Documentation
* Security Context Documentation
* Pod Security Standards Documentation
