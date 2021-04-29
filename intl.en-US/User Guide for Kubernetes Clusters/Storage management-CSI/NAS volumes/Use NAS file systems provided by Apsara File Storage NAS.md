---
keyword: [NAS volumes, limits]
---

# Use NAS file systems provided by Apsara File Storage NAS

Container Service for Kubernetes \(ACK\) allows you to use NAS file systems provided by Apsara File Storage NAS as persistent volumes \(PVs\) in ACK clusters. This topic describes the notes and limits when you use NAS volumes.

NAS file systems can be mounted on an ACK cluster by using the Container Storage Interface \(CSI\) plug-in in two forms:

-   Mount as static PVs
-   Mount as dynamic PVs

## Prerequisites

A NAS file system is created and a mount target is added to the file system. To create a NAS file system and add a mount target, log on to the [NAS console](https://nas.console.aliyun.com/). The mount target of the NAS file system and your cluster are deployed in the same virtual private cloud \(VPC\).

The mount target is in the following format: `055f84ad83-ixxxx.cn-hangzhou.nas.aliyuncs.com`.

## Usage notes

-   Apsara File Storage NAS is a shared storage service. A persistent volume claim \(PVC\) that is used to mount a NAS file system can be shared among pods.
-   Do not delete a mount target before the NAS file system that is mounted is uninstalled. Otherwise, the operating system hang may occur.
-   After a mount target is created, wait until the mount target is ready for use.
-   We recommend that you use NFSv3.
-   We recommend that you upgrade the CSI plug-in to the latest version before you use NAS file systems as PVs.
-   General-purpose and Extreme NAS file systems have different limits on supported mounting scenarios, number of file systems, and file sharing protocols. For more information, see [Limits of Apsara File Storage NAS]().

