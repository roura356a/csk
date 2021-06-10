# Overview

You can mount Object Storage Service \(OSS\) buckets to Container Service for Kubernetes \(ACK\) clusters.

Only statically provisioned OSS buckets can be mounted to ACK clusters.

## Prerequisites

An OSS bucket is created in the OSS console. For more information, see [Create buckets](/intl.en-US/Quick Start/OSS console/Create buckets.md).

## Instruction

The following section describes how to configure a statically provisioned OSS volume:

-   An OSS bucket can be shared by multiple pods.
-   bucket: You can mount only buckets to clusters. The subdirectories or files in a bucket cannot be mounted to an ACK cluster.
-   url: the endpoint of an OSS bucket. If the bucket and the node to which the bucket is mounted are deployed in the same region, you can specify the internal endpoint of an OSS bucket.
-   akId: your AccessKey ID.
-   akSecret: your AccessKey secret.
-   otherOpts: the custom parameters that are used to mount the OSS bucket. The parameters must be in the following format: `-o *** -o ***`.
-   To mount an OSS volume, do not specify subpath.
-   We recommend that you create a persistent volume \(PV\) for each application.
-   Only the CentOS and Alibaba Cloud Linux 2 operating systems are supported.

## Considerations

-   OSS is a Filesystem in Userspace \(FUSE\) file system that can be mounted by using OSSFS. This method is suitable for read operations. For example, you can use this method to read configuration files, video files, and images.
-   OSSFS is not suitable for write operations. If you require write operations, we recommend that you use Apsara File Storage NAS \(NAS\) file systems.
-   Compared with FUSE, the file system in a kernel state offers higher stability and performance. We recommend that you use NAS file systems instead of OSS buckets in production environments.
-   You can modify parameter configurations to optimize OSSFS performance in caching and permission management. For more information, see [FAQ about OSSFS](/intl.en-US/Tools/ossfs/FAQ.md), [ossfs/README-CN.md](https://github.com/aliyun/ossfs/blob/master/README-CN.md), and [FAQ](https://github.com/aliyun/ossfs/wiki/FAQ).

