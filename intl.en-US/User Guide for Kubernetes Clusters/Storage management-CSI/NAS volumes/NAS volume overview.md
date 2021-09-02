---
keyword: [NAS volumes, limits]
---

# NAS volume overview

You can use Apsara File Storage NAS \(NAS\) volumes in Container Service for Kubernetes \(ACK\) clusters. This topic describes the features, types, use scenarios, limits, and billing rules of NAS volumes.

## Features

NAS is a cloud service that provides a file storage solution for compute nodes, such as Elastic Compute Service \(ECS\) instances, Elastic High-Performance Computing \(E-HPC\) clusters, and ACK clusters. NAS is a distributed file storage solution that provides shared access, scalability, high reliability, and high performance.

NAS uses Portable Operating System Interface of UNIX \(POSIX\)-based APIs and is compatible with native operating systems. NAS provides shared access, ensures data consistency, and implements mutual exclusion by using locks. NAS provides scalable file systems and allows simultaneous access to a file system from multiple ECS instances. The storage capacity of the file system scales up or down when you add or remove files. NAS provides shared data sources for workloads and applications that run on multiple ECS instances or servers.

## NAS file system types

NAS provides the following types of file system: general-purpose NAS Capacity, general-purpose NAS Performance, and Extreme NAS. For more information, see [NAS file system types]().

## Scenarios

The following table describes the operations that you can perform on a NAS volume to meet your business requirements.

|Operation|References|
|---------|----------|
|Store application data|For more information, see:

-   [Use a statically provisioned NAS volume](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/NAS volumes/Use a statically provisioned NAS volume.md)
-   [Use a dynamically provisioned NAS volume](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/NAS volumes/Use a dynamically provisioned NAS volume.md) |
|Use quotas to configure resource allocation and improve overall resource utilization|For more information, see [Set quotas on the subdirectories of NAS volumes](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/NAS volumes/Set quotas on the subdirectories of NAS volumes.md). |

## Limits

-   NAS is a shared storage service. A persistent volume claim \(PVC\) that is used to mount a NAS file system can be used by multiple pods.
-   Do not delete the mount target before you unmount the NAS file system. Otherwise, the operating system hang may occur.
-   After a mount target is created, wait until the **state** of the mount target changes to **Available**.
-   We recommend that you use NFSv3.
-   Before you use NAS volumes, we recommend that you upgrade the Container Storage Interface \(CSI\) plug-in to the latest version.
-   General-purpose and Extreme NAS file systems have different limits on mounting scenarios, the number of file systems, and file sharing protocols. For more information, see [Limits of Apsara File Storage NAS]().

## Billing rules

For more information about the billing rules of NAS, see [NAS billing]().

