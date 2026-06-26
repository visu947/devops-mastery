                kubectl
                    │
                    ▼
           kube-apiserver
                    │
      ┌─────────────┼─────────────┐
      ▼             ▼             ▼
    etcd      Scheduler    Controller Manager
                    │
         ┌──────────┴──────────┐
         ▼                     ▼
    Worker Node          Worker Node
         │                     │
     kubelet              kubelet
     kube-proxy           kube-proxy
     containerd           containerd