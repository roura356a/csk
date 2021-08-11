---
keyword: [NAS volumes, static volumes, dynamic volumes]
---

# Use NAS volumes

This topic describes how to mount Network Attached Storage \(NAS\) file systems to clusters of Container Service for Kubernetes \(ACK\) as volumes and how to use NAS volumes.

## Prerequisites

A NAS file system is created and a mount target is added to the file system. To create a NAS file system and add a mount target, log on to the [NAS console](https://nas.console.aliyun.com/). The mount target of the NAS file system and your cluster are deployed in the same virtual private cloud \(VPC\).

The mount target is in the following format: `055f84ad83-ixxxx.cn-hangzhou.nas.aliyuncs.com`.

## Background information

You can mount file systems of Apsara File Storage NAS to ACK clusters in the following ways:

-   Mount NAS file systems as static volumes
    -   Directly mount NAS file systems as volumes.
    -   Use a pair of persistent volume \(PV\) and persistent volume claim \(PVC\) to mount NAS file systems.
-   Mount NAS file systems as dynamic volumes

## Scenarios

-   Static volumes

    NAS provides shared storage services. You can mount NAS file systems as static volumes to meet the requirements of diverse scenarios.

-   Dynamic volumes

    You can mount NAS file systems as dynamic volumes when you need to use multiple NAS sub-directories for different applications.

    You can also mount NAS file systems as dynamic volumes when you use the StatefulSet controller to deploy applications and want each pod to use a separate NAS volume.


## How to mount NAS file systems

We recommend that you read the following information before you mount NAS file systems to ACK clusters:

-   Recommended volume plug-in

    We recommend that you use the Flexvolume driver to mount NAS file systems.

    The Flexvolume driver is installed by default when you create an ACK cluster in the console. You must make sure that the Flexvolume driver is upgraded to the latest version. For more information, see [Upgrade the Flexvolume driver](/intl.en-US/User Guide for Kubernetes Clusters/Component/Manage system components.md).

-   Recommended mounting method

    We recommend that you mount a NAS file system by using a pair of PV and PVC. This makes the NAS file system easier to manage and maintain.

    -   For more information about static volumes, see [Statically provisioned NAS volumes](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/NAS volumes/Statically provisioned NAS volumes.md).
    -   For more information about dynamic volumes, see [Dynamic NAS volumes](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/NAS volumes/Dynamic NAS volumes.md).
-   Not recommended mounting method

    We recommend that you do not directly mount NAS file systems as volumes. You can use only the Flexvolume driver to mount volumes to ACK clusters. The Network File System \(NFS\) driver provided by Kubernetes is not supported.


## Considerations

-   NAS is a shared storage system that provides storage services for multiple pods at a time. A PVC can be shared among multiple pods.
-   Do not delete a mount target if the related NAS file system is still mounted. Otherwise, the operating system may become unresponsive.
-   After a mount target is created, wait until the mount target is **Available** for use.
-   We recommend that you use NFS v3.
-   We recommend that you upgrade Flexvolume to the latest version before you use NAS volumes.
-   NAS file systems of Extreme type support only NFS v3. You must specify the nolock parameter when you mount these file systems.

