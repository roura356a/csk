---
keyword: [volume plug-in, Flexvolume, CSI]
---

# Volume plug-ins

Clusters of Container Service for Kubernetes \(ACK\) support the Flexvolume and CSI plug-ins. This topic describes the features of these plug-ins and how to select between them based on your requirements.

## Differences between FlexVolume and CSI

|Plug-in|Feature|Related topic|
|-------|-------|-------------|
|FlexVolume|FlexVolume is a traditional mechanism to extend Kubernetes storage systems developed by the Kubernetes community. ACK supports FlexVolume. FlexVolume consists of the following parts: -   FlexVolume: Allows you to mount and delete volumes and PVs. By default, ACK allows you to mount the following types of storage media: disks, Network Storage Service \(NAS\) file systems, and Object Storage Service \(OSS\) buckets.
-   Disk-Controller: Automatically creates disks.
-   Nas-Controller: Automatically creates NAS file systems.

|For more information about FlexVolume, see [Overview](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Overview.md).

For more information about upgrading FlexVolume, see [Manage system components](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Upgrade cluster/Manage system components.md). |
|CSI|CSI is recommended by the Kubernetes community. The CSI plug-in provided by ACK is compatible with the features of its community version. CSI consists of the following parts: -   CSI-Plugin: Allows you to mount and delete volumes and PVs. By default, ACK allows you to mount the following types of storage media: disks, NAS file systems, and OSS buckets.
-   CSI-Provisioner: Automatically creates volumes, PVs, and related disks or NAS file systems.

|For more information about CSI, see [Overview](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Overview.md) and [alibaba-cloud-csi-driver](https://github.com/kubernetes-sigs/alibaba-cloud-csi-driver). |

## Recommendations

-   For newly created ACK clusters, we recommend that you use CSI. The ACK technical team will continue to upgrade CSI to support more features of its community version.
-   For existing ACK clusters, we recommend that you use the preinstalled plug-in. The ACK technical team will continue to support FlexVolume.

## Considerations

-   You need to select a plug-in when you create an ACK cluster.
-   You cannot use both plug-ins on the same ACK cluster.
-   You cannot change the plug-in from FlexVolume to CSI for an ACK cluster.

