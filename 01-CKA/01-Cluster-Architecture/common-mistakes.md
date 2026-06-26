# Common Mistakes

## Mistake 1

Thinking kubelet schedules Pods.

Reality:

The Scheduler chooses the node.

The kubelet only runs Pods assigned to its node.

---

## Mistake 2

Thinking etcd stores container images.

Reality:

etcd stores metadata only.

Images remain in container registries.

---

## Mistake 3

Thinking kube-proxy forwards traffic itself.

Reality:

kube-proxy configures networking rules.

The Linux kernel handles packet forwarding.