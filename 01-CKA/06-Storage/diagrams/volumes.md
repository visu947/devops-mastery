# Volumes

```mermaid
flowchart TD
    Pod[Pod]
    Container[Container]
    Volume[Volume]
    MountPath["/data mountPath"]

    Pod --> Container
    Pod --> Volume
    Container --> MountPath
    MountPath --> Volume
