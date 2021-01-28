# Overview

Clusters of Container Service for Kubernetes \(ACK\) can be automatically bound to cloud disks of Alibaba Cloud, Network Attached Storage \(NAS\) file systems, and Object Storage Service \(OSS\) buckets that are mounted to pods.

The Container Storage Interface \(CSI\) driver supports both static and dynamic PVs. The following table describes the support for static and dynamic PVs by each storage service.

|Alibaba Cloud storage|Static PV|Dynamic PV|
|---------------------|---------|----------|
|Alibaba Cloud disk|The CSI driver supports to mount an Alibaba Cloud disk as a static PV. You can mount an Alibaba Cloud disk by using a PV and a PVC.

|The CSI driver supports to mount an Alibaba Cloud disk as a dynamic PV.|
|Apsara File Storage NAS|The CSI driver supports to mount a NAS file system provided by Apsara File Storage NAS as a static PV. You can mount a NAS file system by using a PV and a PVC.

|The CSI driver supports to mount a NAS file system provided by Apsara File Storage NAS as a dynamic PV.|
|OSS|The CSI driver supports to mount an OSS bucket as a static PV. You can mount an OSS bucket by using a PV and a PVC.

|The CSI driver does not support to mount an OSS bucket as a dynamic PV.|

## Kubernetes versions

-   FlexVolume and CSI are available for clusters of Kubernetes 1.14 and later.
-   If you select the CSI driver when you create an ACK cluster, set `enable-controller-attach-detach` to true for kubelet.

The regions supported by the CSI driver are the same as those available for Kubernetes 1.14.

