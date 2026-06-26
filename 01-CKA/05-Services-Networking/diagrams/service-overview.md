# Service Overview

```mermaid
flowchart TD
    Client[Client]
    Ingress[Ingress]
    Service[Service]
    EndpointSlice[EndpointSlice]
    Pod1[Pod 1]
    Pod2[Pod 2]
    Container1[Container]
    Container2[Container]

    Client --> Ingress
    Ingress --> Service
    Service --> EndpointSlice
    EndpointSlice --> Pod1
    EndpointSlice --> Pod2
    Pod1 --> Container1
    Pod2 --> Container2
```

## Key Point

Services provide stable access to dynamic Pods.
