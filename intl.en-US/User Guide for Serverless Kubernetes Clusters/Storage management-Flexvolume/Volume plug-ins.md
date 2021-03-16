---
keyword: [volume plug-ins, FlexVolume, CSI]
---

# Volume plug-ins

Serverless Kubernetes \(ASK\) clusters support the FlexVolume and Container Storage Interface \(CSI\) plug-ins. This topic describes the features of these plug-ins and how to choose a plug-in to meet your requirements.

## Differences between FlexVolume and CSI

|Plug-in|Feature|Reference|
|-------|-------|---------|
|Flexvolume|FlexVolume is the traditional open source volume plug-in that is used to extend Kubernetes storage systems. ASK clusters support the FlexVolume plug-in. The FlexVolume plug-in consists of the following parts: -   FlexVolume: allows you to mount and unmount volumes. By default, ASK allows you to mount Alibaba Cloud disks and Apsara File Storage NAS \(NAS\) file systems as volumes.
-   Disk-Controller: automatically mounts disks as volumes.
-   Nas-Controller: automatically mounts NAS file systems as volumes.

|For more information about FlexVolume, see [Overview](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Overview.md).

For more information about how to upgrade FlexVolume, see [Manage system components](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Upgrade cluster/Manage system components.md). |
|CSI|CSI is recommended by the Kubernetes community. The CSI plug-in used in ASK clusters supports the features of the open source CSI version. CSI consists of the following parts: -   CSI-Plugin: allows you to mount and unmount volumes. By default, ASK allows you to mount Alibaba Cloud disks and Apsara File Storage NAS \(NAS\) file systems as volumes.
-   CSI-Provisioner: automatically mounts disks and NAS file systems as volumes.

|For more information about CSI, see [Overview](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Overview.md) and [alibaba-cloud-csi-driver](https://github.com/kubernetes-sigs/alibaba-cloud-csi-driver).

有关如何升级CSI存储插件，请参见[CSI存储插件升级指南](https://yq.aliyun.com/articles/745944)。 |

## How to choose

-   For newly created clusters, we recommend that you use CSI. ASK will continue to upgrade CSI to support more features of the open source CSI.
-   For existing clusters, we recommend that you use the plug-in that you have installed. ASK will continue to support FlexVolume.

## Considerations

-   You need to select a plug-in when you create an ACK cluster.
-   You cannot use both plug-ins on the same ACK cluster.
-   You cannot change the plug-in from FlexVolume to CSI for an ACK cluster.

