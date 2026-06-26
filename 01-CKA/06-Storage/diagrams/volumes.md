# Volumes

```mermaid
flowchart TD
    Pod[Pod]
    Container[Container]
    Volume[Volume]
    MountPath[/data]

    Pod --> Container
    Pod --> Volume
    Container --> MountPath
    MountPath --> Volume
```

## Key Point

A Volume is mounted into a container at a specific path and can outlive individual container restarts.
