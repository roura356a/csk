# Overview

Clusters of Container Service for Kubernetes \(ACK\) can be automatically bound to Alibaba Cloud disks, Apsara File Storage NAS \(NAS\) file systems, and Object Storage Service \(OSS\) buckets that are mounted to pods.

The Container Storage Interface \(CSI\) driver supports both static and dynamic volumes. The following table describes the support for static and dynamic volumes by each storage service.

|Alibaba Cloud storage|Static volume|Dynamic volume|
|---------------------|-------------|--------------|
|Alibaba Cloud disk|The CSI driver supports mounting an Alibaba Cloud disk as a static volume.You can mount an Alibaba Cloud disk by using a persistent volume \(PV\) and a persistent volume claim \(PVC\).

|The CSI driver supports mounting an Alibaba Cloud disk as a dynamic volume.|
|NAS file system|The CSI driver supports mounting a NAS file system as a static volume.You can mount a NAS file system by using a PV and a PVC.

|The CSI driver does not support mounting a NAS file system as a dynamic volume.|
|OSS bucket|The CSI driver supports mounting an OSS bucket as a static volume.You can mount an OSS bucket by using a PV and a PVC.

|The CSI driver does not support mounting an OSS bucket as a dynamic volume.|

## Kubernetes versions

-   FlexVolume and CSI are available for clusters of Kubernetes 1.14 and later.
-   If you select the CSI driver when you create an ACK cluster, set `enable-controller-attach-detach` to true for kubelet.

The regions supported by the CSI driver are the same as those available for Kubernetes 1.14.

