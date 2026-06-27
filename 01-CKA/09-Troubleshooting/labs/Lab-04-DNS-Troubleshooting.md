# Lab 04 - DNS Troubleshooting

## Difficulty

⭐⭐⭐⭐ Intermediate

## Estimated Time

35–45 minutes

---

# CKA Objectives Covered

* Troubleshoot Kubernetes DNS
* Verify CoreDNS health
* Test Service name resolution
* Verify DNS configuration inside Pods
* Diagnose FQDN issues
* Validate DNS connectivity

---

# Objective

In this lab, you will troubleshoot common DNS issues including:

* Service name resolution failures
* CoreDNS not running
* Missing kube-dns Service
* Incorrect `/etc/resolv.conf`
* Namespace-related DNS problems
* NetworkPolicy blocking DNS

Your goal is to restore successful DNS resolution inside the cluster.

---

# Architecture

```mermaid id="dns01"
flowchart LR

Client[Client Pod]

CoreDNS[CoreDNS]

KubeDNS[kube-dns Service]

Service[Application Service]

Backend[Application Pod]

Client --> KubeDNS
KubeDNS --> CoreDNS
CoreDNS --> Service
Service --> Backend
```

---

# DNS Troubleshooting Workflow

```text id="dns02"
DNS Failure

↓

Check CoreDNS Pods

↓

Check kube-dns Service

↓

Launch Test Pod

↓

Test DNS Resolution

↓

Check resolv.conf

↓

Test FQDN

↓

Check NetworkPolicy

↓

Apply Fix

↓

Verify
```

---

# Scenario 1 - Service Name Does Not Resolve

## Symptoms

```text id="dns03"
nslookup my-service

server can't find my-service
```

---

## Investigation

Launch a temporary Pod:

```bash id="dns04"
kubectl run dns-test \
--image=busybox:1.36 \
-it --rm \
--restart=Never -- sh
```

Inside:

```sh id="dns05"
nslookup my-service
```

---

# Scenario 2 - CoreDNS Not Running

## Investigation

```bash id="dns06"
kubectl get pods -n kube-system
```

Verify CoreDNS Pods are:

```text id="dns07"
Running
```

If not:

```bash id="dns08"
kubectl describe pod -n kube-system <coredns-pod>

kubectl logs -n kube-system <coredns-pod>
```

---

# Scenario 3 - kube-dns Service Missing

## Investigation

```bash id="dns09"
kubectl get svc -n kube-system
```

Expected:

```text id="dns10"
kube-dns
```

Verify:

* ClusterIP
* Port 53
* Endpoints

---

# Scenario 4 - Incorrect Namespace

## Investigation

Inside the test Pod:

```sh id="dns11"
nslookup my-service
```

If the Service is in another namespace:

```sh id="dns12"
nslookup my-service.production
```

or the full FQDN:

```sh id="dns13"
nslookup my-service.production.svc.cluster.local
```

---

# Scenario 5 - Verify /etc/resolv.conf

Inside the Pod:

```sh id="dns14"
cat /etc/resolv.conf
```

Expected output includes:

```text id="dns15"
search

svc.cluster.local

cluster.local
```

and a valid nameserver pointing to the cluster DNS Service.

---

# Scenario 6 - Test FQDN Resolution

Inside the Pod:

```sh id="dns16"
nslookup kubernetes.default.svc.cluster.local
```

Expected:

DNS resolves successfully.

---

# Scenario 7 - NetworkPolicy Blocking DNS

## Investigation

```bash id="dns17"
kubectl get networkpolicy
```

Describe the policy:

```bash id="dns18"
kubectl describe networkpolicy <policy-name>
```

Review whether UDP/TCP port 53 traffic is allowed.

---

# Scenario 8 - Verify DNS Service Endpoints

```bash id="dns19"
kubectl get endpoints -n kube-system
```

Verify the `kube-dns` Service has healthy backend Pods.

---

# Useful Commands

```bash id="dns20"
kubectl get pods -n kube-system

kubectl get svc -n kube-system

kubectl get endpoints -n kube-system

kubectl logs -n kube-system deployment/coredns

kubectl describe pod -n kube-system <coredns-pod>

kubectl run dns-test \
--image=busybox:1.36 \
-it --rm \
--restart=Never -- sh
```

---

# Verification Checklist

✅ CoreDNS Pods healthy.

✅ kube-dns Service exists.

✅ DNS resolution tested.

✅ FQDN verified.

✅ `/etc/resolv.conf` verified.

✅ NetworkPolicy reviewed.

✅ DNS functioning correctly.

---

# Common Mistakes

❌ Assuming every networking issue is a DNS issue.

❌ Forgetting that short Service names work only within the same namespace.

❌ Ignoring `/etc/resolv.conf`.

❌ Forgetting to verify the `kube-dns` Service.

❌ Testing only from outside the cluster instead of from a Pod.

---

# Production Discussion

A good DNS troubleshooting process is:

1. Verify CoreDNS Pods.
2. Verify the `kube-dns` Service.
3. Launch a temporary test Pod.
4. Test short Service names.
5. Test FQDNs.
6. Inspect `/etc/resolv.conf`.
7. Review NetworkPolicy.
8. Confirm DNS recovery.

This approach isolates DNS problems from general networking issues.

---

# Knowledge Check

1. Which Pods provide DNS for the cluster?
2. What Service exposes cluster DNS?
3. How do you test DNS from inside the cluster?
4. Why might `my-service` resolve in one namespace but not another?
5. What information should `/etc/resolv.conf` contain?

---

# Challenge

Applications in the `frontend` namespace cannot resolve backend Services.

Investigate the following:

* CoreDNS Pods
* `kube-dns` Service
* Service FQDN
* `/etc/resolv.conf`
* DNS endpoints
* NetworkPolicy allowing DNS traffic

For each issue:

1. Identify the troubleshooting commands.
2. Determine the root cause.
3. Apply the appropriate fix.
4. Verify successful DNS resolution.
5. Explain the difference between short Service names and fully qualified domain names (FQDNs).
