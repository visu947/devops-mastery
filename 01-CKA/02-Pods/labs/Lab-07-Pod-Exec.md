# Lab 07 - Executing Commands Inside a Pod

## Difficulty

⭐⭐ Intermediate

## Estimated Time

25–35 minutes

---

# CKA Objectives Covered

* Execute commands inside containers
* Inspect container file systems
* Verify environment variables
* Troubleshoot running applications

---

# Objective

In this lab, you will:

* Open an interactive shell inside a Pod.
* Execute commands within a running container.
* Inspect files and directories.
* View environment variables.
* Understand common production debugging techniques.

---

# Architecture

```mermaid
flowchart LR

Administrator --> kubectl

kubectl --> API[API Server]

API --> kubelet

kubelet --> Container

Container --> Shell[/bin/sh or /bin/bash]
```

---

# What is `kubectl exec`?

`kubectl exec` allows you to execute commands inside a running container.

It is commonly used for:

* Debugging
* Inspecting configuration
* Viewing files
* Checking networking
* Verifying mounted volumes
* Testing connectivity

---

# Step 1 - Create a Pod

```bash
kubectl run nginx --image=nginx
```

Verify:

```bash
kubectl get pods
```

Wait until the Pod is in the `Running` state.

---

# Step 2 - Open an Interactive Shell

Try Bash first:

```bash
kubectl exec -it nginx -- /bin/bash
```

If Bash is unavailable:

```bash
kubectl exec -it nginx -- /bin/sh
```

---

# Step 3 - Explore the Container

Run the following commands:

```bash
hostname

pwd

whoami

ls /

ls -la

cat /etc/os-release
```

Observe:

* Hostname
* Current directory
* Linux distribution
* File system layout

---

# Step 4 - View Environment Variables

```bash
env
```

Look for:

* PATH
* HOSTNAME
* Kubernetes-generated environment variables

---

# Step 5 - Verify Running Processes

```bash
ps
```

or

```bash
ps aux
```

Observe the running NGINX processes.

---

# Step 6 - Verify DNS Resolution

Install BusyBox Pod (optional):

```bash
kubectl run busybox \
  --image=busybox \
  -it \
  --rm \
  -- sh
```

Inside BusyBox:

```bash
nslookup kubernetes.default

ping kubernetes.default
```

Observe Kubernetes DNS resolution.

---

# Step 7 - Execute a Single Command

Instead of opening a shell:

```bash
kubectl exec nginx -- ls /etc
```

Another example:

```bash
kubectl exec nginx -- cat /etc/os-release
```

This is useful for automation scripts.

---

# Step 8 - Multi-Container Pod

For a multi-container Pod:

```bash
kubectl exec -it multi-container-pod \
  -c nginx \
  -- sh
```

Or:

```bash
kubectl exec -it multi-container-pod \
  -c busybox \
  -- sh
```

Always specify:

```text
-c <container-name>
```

when multiple containers exist.

---

# Verification Checklist

✅ Opened an interactive shell.

✅ Executed commands.

✅ Viewed environment variables.

✅ Viewed running processes.

✅ Verified DNS.

✅ Used `kubectl exec` on a multi-container Pod.

---

# Common Errors

## Container Not Running

```bash
kubectl get pods
```

A container must be running before you can execute commands inside it.

---

## Bash Not Found

Many container images do not include Bash.

Use:

```bash
/bin/sh
```

instead.

---

## Wrong Container

For multi-container Pods:

```bash
kubectl exec -it <pod> \
  -c <container> \
  -- sh
```

---

# Production Discussion

Common uses of `kubectl exec`:

* Inspect mounted ConfigMaps
* Verify mounted Secrets
* Check application configuration
* Test DNS resolution
* Verify file permissions
* Inspect temporary files
* Debug networking

Avoid making manual configuration changes inside containers, as they are ephemeral and will be lost when the Pod is recreated.

---

# Production Tips

* Use `kubectl exec` for investigation, not permanent fixes.
* Apply changes through manifests or container images.
* Record findings before restarting or deleting Pods.

---

# Knowledge Check

1. What is the purpose of `kubectl exec`?
2. Why might `/bin/bash` not be available?
3. How do you execute a command without opening a shell?
4. Why must you specify `-c` in a multi-container Pod?
5. Why are manual changes inside a container temporary?

---

# Cleanup

```bash
kubectl delete pod nginx

kubectl delete pod busybox --ignore-not-found
```

---

# Challenge

1. Create an NGINX Pod.
2. Open a shell inside the Pod.
3. Display the operating system information.
4. List the root directory.
5. View environment variables.
6. Run a single command using `kubectl exec` without opening a shell.
7. Repeat the exercise on a multi-container Pod.
