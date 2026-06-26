# Kubernetes QoS Classes

```mermaid
flowchart TD
    Pod[Pod Resources]

    Pod --> Guaranteed[Guaranteed]
    Pod --> Burstable[Burstable]
    Pod --> BestEffort[BestEffort]

    Guaranteed --> GRule[Requests = Limits for CPU and Memory]
    Burstable --> BRule[Requests set but lower than Limits]
    BestEffort --> BERule[No Requests or Limits]

    Guaranteed --> GEvict[Least likely to be evicted]
    Burstable --> BEvict[Medium eviction priority]
    BestEffort --> BEEvict[Most likely to be evicted]
```

## Summary

| QoS Class | Condition | Eviction Priority |
|---|---|---|
| Guaranteed | Requests equal limits | Lowest |
| Burstable | Requests lower than limits | Medium |
| BestEffort | No requests or limits | Highest |
