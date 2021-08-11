---
keyword: [NAS volumes, limits]
---

# Overview

Container Service for Kubernetes \(ACK\) allows you to mount Apsara File Storage NAS \(NAS\) file systems as persistent volumes \(PVs\) in ACK clusters. This topic describes the limits of NAS volumes and provides usage notes for NAS volumes.

NAS file systems can be mounted to an ACK cluster by using the Container Storage Interface \(CSI\) plug-in in two forms:

-   Mount as statically provisioned PVs
-   Mount as dynamically provisioned PVs

## Prerequisites

A NAS file system is created and a mount target is added to the file system. To create a NAS file system and add a mount target, log on to the [NAS console](https://nas.console.aliyun.com/). The mount target of the NAS file system and your cluster are deployed in the same virtual private cloud \(VPC\).

The mount target is in the following format: `055f84ad83-ixxxx.cn-hangzhou.nas.aliyuncs.com`.

## Usage notes

-   Apsara File Storage NAS is a shared storage service. A persistent volume claim \(PVC\) that is used to mount a NAS file system can be shared among pods.
-   Do not delete the mount target before you unmount the NAS file system. Otherwise, the operating system hang may occur.
-   After a mount target is created, wait until the mount target is **Available** for use.
-   We recommend that you use NFSv3.
-   We also recommend that you upgrade the CSI plug-in to the latest version before you mount NAS file systems as PVs.
-   General-purpose and Extreme NAS file systems have different limits on mounting scenarios, the number of file systems, and file sharing protocols. For more information, see [Limits of Apsara File Storage NAS]().

