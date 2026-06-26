# Troubleshooting

## Node NotReady

Possible causes

- kubelet stopped
- network failure
- disk pressure

Commands

kubectl describe node

journalctl -u kubelet

---

## API Server unavailable

Symptoms

kubectl hangs

Possible causes

API Server down

Certificate issues

Firewall

---

## Scheduler failure

Symptoms

Pods remain Pending

Commands

kubectl describe pod

kubectl get events