# Overview {#concept_vgg_45s_vdb .concept}

Container Service supports automatically binding Kubernetes pods to Alibaba Cloud cloud disks, NAS, and Object Storage Service \(OSS\).

Currently, static storage volumes and dynamic storage volumes are supported. See the following table for how each type of data volumes supports the static data volumes and dynamic data volumes.

|Alibaba Cloud storage|Static data volume|Dynamic data volume|
|---------------------|------------------|-------------------|
|Alibaba Cloud cloud disk|You can use the cloud disk static storage volumes by:-   Using the volume method.
-   Using PV/PVC.

|Supported.|
|Alibaba Cloud NAS|You can use the NAS static storage volumes by:-   Using flexvolume plug-in.
    -   Using the volume method.
    -   Using PV/PVC.
-   Using NFS drive of Kubernetes.

|Supported.|
|Alibaba Cloud OSS|You can use the OSS static storage volumes by:-   Using the volume method.
-   Using PV/PVC.

|Not supported.|

