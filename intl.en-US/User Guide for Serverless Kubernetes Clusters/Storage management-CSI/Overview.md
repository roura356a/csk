# Overview

Container Service can automatically bind Kubernetes pods to Alibaba Cloud disks, Network Attached Storage \(NAS\), and Object Storage Service \(OSS\).

Currently, the Container Storage Interface \(CSI\) driver supports both static and dynamic volumes. The following table describes the support for static and dynamic volumes by each storage service.

|Alibaba Cloud storage|Static volume|Dynamic volume|
|---------------------|-------------|--------------|
|Alibaba Cloud disk|Static volumes attached through the CSI driver are supported. You can use a static volume by creating a persistent volume \(PV\) and a persistent volume claim \(PVC\).

|Supported|
|Alibaba Cloud NAS|Static volumes attached through the CSI driver are supported. You can use a static volume by creating a PV and a PVC.

|Supported|
|Alibaba Cloud OSS|Static volumes attached through the CSI driver are supported. You can use a static volume by creating a PV and a PVC.

|Not supported|

## Supported versions

-   When Kubernetes 1.14 or a later version is used, the FlexVolume and CSI plug-ins are supported.
-   If you select the CSI driver when creating a cluster, set the kubelet parameter `enable-controller-attach-detach` to true.

The regions supported by the CSI driver are the same as those available for Kubernetes 1.14.

