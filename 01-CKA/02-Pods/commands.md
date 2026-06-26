# Pods - Commands

This document contains the most commonly used commands for creating, inspecting, debugging, and managing Pods.

---

# Verify Cluster

Before working with Pods, verify the cluster is healthy.

```bash
kubectl cluster-info
kubectl get nodes
kubectl get nodes -o wide
```

---

# Create Pods

## Create an NGINX Pod

```bash
kubectl run nginx --image=nginx
```

---

## Create Pod with Custom Port

```bash
kubectl run nginx \
  --image=nginx \
  --port=80
```

---

## Generate Pod YAML

Generate YAML without creating the Pod.

```bash
kubectl run nginx \
  --image=nginx \
  --dry-run=client \
  -o yaml
```

---

## Save YAML to File

```bash
kubectl run nginx \
  --image=nginx \
  --dry-run=client \
  -o yaml > pod.yaml
```

---

## Create Pod from YAML

```bash
kubectl apply -f pod.yaml
```

---

# View Pods

## List Pods

```bash
kubectl get pods
```

---

## List Pods with More Information

```bash
kubectl get pods -o wide
```

---

## Watch Pod Status

```bash
kubectl get pods -w
```

---

## Show Pod Labels

```bash
kubectl get pods --show-labels
```

---

## Output Pod as YAML

```bash
kubectl get pod nginx -o yaml
```

---

## Output Pod as JSON

```bash
kubectl get pod nginx -o json
```

---

# Describe Pods

## Describe a Pod

```bash
kubectl describe pod nginx
```

Useful for viewing:

- Events
- Mounted volumes
- Resource limits
- Conditions
- Container status

---

# Logs

## View Logs

```bash
kubectl logs nginx
```

---

## Follow Logs

```bash
kubectl logs -f nginx
```

---

## Previous Container Logs

```bash
kubectl logs nginx --previous
```

Useful for debugging CrashLoopBackOff.

---

## Multi-Container Pod Logs

```bash
kubectl logs mypod -c app
```

---

# Execute Commands

## Open Shell

```bash
kubectl exec -it nginx -- /bin/bash
```

If Bash is unavailable:

```bash
kubectl exec -it nginx -- /bin/sh
```

---

## Run a Command

```bash
kubectl exec nginx -- ls /
```

---

# Copy Files

Copy from Pod to Local

```bash
kubectl cp nginx:/etc/nginx/nginx.conf .
```

---

Copy Local File to Pod

```bash
kubectl cp config.yaml nginx:/tmp/
```

---

# Port Forwarding

Forward local port 8080 to Pod port 80.

```bash
kubectl port-forward pod/nginx 8080:80
```

Now browse:

```
http://localhost:8080
```

---

# Labels

Add Label

```bash
kubectl label pod nginx app=web
```

---

Update Label

```bash
kubectl label pod nginx app=frontend --overwrite
```

---

Remove Label

```bash
kubectl label pod nginx app-
```

---

# Annotations

Add Annotation

```bash
kubectl annotate pod nginx owner=viswanad
```

---

Remove Annotation

```bash
kubectl annotate pod nginx owner-
```

---

# Edit Pod

```bash
kubectl edit pod nginx
```

Note:

Many Pod fields are immutable.

---

# Delete Pods

Delete Pod

```bash
kubectl delete pod nginx
```

---

Delete Immediately

```bash
kubectl delete pod nginx --force --grace-period=0
```

---

Delete All Pods

```bash
kubectl delete pods --all
```

---

# Namespaces

Pods in Namespace

```bash
kubectl get pods -n kube-system
```

---

Create Pod in Namespace

```bash
kubectl run nginx \
  --image=nginx \
  -n development
```

---

# Debugging

Describe Pod

```bash
kubectl describe pod nginx
```

---

Check Events

```bash
kubectl get events --sort-by=.lastTimestamp
```

---

View Resource Usage

Requires Metrics Server.

```bash
kubectl top pod
```

---

View Node Usage

```bash
kubectl top node
```

---

# Common Output Formats

```bash
kubectl get pods -o yaml
kubectl get pods -o json
kubectl get pods -o wide
kubectl get pods -A
```

---

# CKA Shortcuts

Create Pod

```bash
kubectl run nginx --image=nginx
```

Generate YAML

```bash
kubectl run nginx \
  --image=nginx \
  --dry-run=client \
  -o yaml
```

Quick Alias

```bash
alias k=kubectl
```

---

# Production Tips

- Prefer `kubectl apply` over `kubectl create` for declarative management.
- Avoid `--force` unless absolutely necessary.
- Use labels consistently for application grouping.
- Use `kubectl describe` before checking logs.
- Use `kubectl logs --previous` for restarting containers.
- Use `kubectl get events` when troubleshooting scheduling issues.

---

# References

- Kubernetes Pods Documentation
- kubectl Command Reference
- CKA Curriculum
