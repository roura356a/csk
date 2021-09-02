---
keyword: [OSS volume, overview]
---

# OSS volume overview

You can use Object Storage Service \(OSS\) volumes in Container Service for Kubernetes \(ACK\) clusters. This topic describes the features, storage classes, use scenarios, limits, and billing rules of OSS volumes.

Only statically provisioned OSS volumes can be mounted to ACK clusters. Dynamically provisioned OSS volumes are not supported.

## Features

OSS is a secure, cost-effective, and high-durability cloud storage service provided by Alibaba Cloud. OSS allows you to store large amounts of data in the cloud.

OSS provides rich security capabilities, including server-side encryption, client-side encryption, hotlink protection whitelists, fine-grained permission control, log auditing, and retention policies \(WROM\). OSS provides complete security protection for your data stored in Alibaba Cloud to meet your security and compliance requirements on enterprise data.

## OSS storage classes

OSS provides the following storage classes to cover a variety of hot and cold data storage scenarios: Standard, Infrequent Access \(IA\), Archive, and Cold Archive. For more information, see [Overview](/intl.en-US/Developer Guide/Storage classes/Overview.md).

## Scenarios

You can perform the following operations on an OSS volume to meet your business requirements.

|Operation|References|
|---------|----------|
|Store application data|For more information, see [Mount a statically provisioned OSS volume](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/OSS volumes/Mount a statically provisioned OSS volume.md). |
|Encrypt data stored in an OSS volume|ACK supports only the server-side encryption of OSS. For more information, see [Server-side encryption](/intl.en-US/Developer Guide/Data security/Data encryption/Server-side encryption.md).

For more information, see [Encrypt an OSS volume](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/OSS volumes/Encrypt an OSS volume.md). |

## Limits

The following section describes how to configure a statically provisioned OSS volume:

-   An OSS bucket can be shared by multiple pods.
-   To mount an OSS volume, do not specify subpath.
-   We recommend that you create a persistent volume \(PV\) for each application.
-   Only the CentOS, Alibaba Cloud Linux 2, and Alibaba Cloud Linux 3 operating systems are supported.
-   OSS is a Filesystem in Userspace \(FUSE\) file system that can be mounted by using OSSFS. This method is suitable for read operations. For example, you can use this method to read configuration files, video files, and images.
-   OSSFS is not suitable for write operations. If you require write operations, we recommend that you use Apsara File Storage NAS \(NAS\) file systems.
-   Compared with file systems in user space, file systems in kernel space offer higher stability and performance. We recommend that you use NAS file systems instead of OSS buckets in production environments.
-   You can modify parameter configurations to optimize OSSFS performance in caching and permission management. For more information, see [FAQ about OSSFS](/intl.en-US/Tools/ossfs/FAQ.md), [ossfs/README-CN.md](https://github.com/aliyun/ossfs/blob/master/README-CN.md), and [FAQ](https://github.com/aliyun/ossfs/wiki/FAQ).

## Billing rules

For more information about the billing rules of OSS, see [Overview](/intl.en-US/Pricing/Billing items and methods/Overview.md).

