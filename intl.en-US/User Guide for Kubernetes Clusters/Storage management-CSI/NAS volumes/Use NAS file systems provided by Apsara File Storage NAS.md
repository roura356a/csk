# Use NAS file systems provided by Apsara File Storage NAS

This topic describes how to use NAS file systems provided by Apsara File Storage NAS as persistent volumes \(PVs\) in a cluster of Container Service for Kubernetes \(ACK\).

NAS file systems can be mounted to an ACK cluster by using the following methods:

-   Mount as static PVs
-   Mount as dynamic PVs

## Prerequisites

A NAS file system is created and a mount target is added to the file system. To create a NAS file system and add a mount target, log on to the [NAS console](https://nas.console.aliyun.com/). The mount target of the NAS file system and your cluster are deployed in the same virtual private cloud \(VPC\).

The mount target is in the following format: `055f84ad83-ixxxx.cn-hangzhou.nas.aliyuncs.com`.

## Notes

-   Apsara File Storage NAS is a shared storage service. A PVC that is used to mount a NAS file system can be shared among pods.
-   Do not delete a mount target before the related NAS file system is uninstalled. Otherwise, the operating system hang may occur.
-   After a mount target is created, wait until the mount target is ready for use.
-   We recommend that you use NFSv3.
-   We recommend that you upgrade the CSI plug-in to the latest version before you use NAS file systems as PVs.

