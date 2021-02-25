# Provision OSS buckets as PVs

You can provision Object Storage Service \(OSS\) buckets as persistent volumes \(PVs\) in Container Service for Kubernetes \(ACK\) clusters.

You can provision OSS buckets only as static PVs.

## Prerequisites

A bucket is created in the OSS console.

## Considerations

-   OSS is a shared storage system that can provide storage services to multiple pods at the same time.
-   bucket: Only buckets can be mounted to an ACK cluster. The subdirectories or files in a bucket cannot be mounted to an ACK cluster.
-   url: the endpoint of the OSS bucket. If the node to which the OSS bucket is mounted is in the same region as the OSS bucket, you can use the internal endpoint of the OSS bucket.
-   akId: your AccessKey ID.
-   akSecret: your AccessKey secret.
-   otherOpts: the custom parameters used to mount the OSS bucket. The parameters are provided in the format of `-o *** -o ***`.
-   To use OSS buckets to create PVs, you do not need to specify a subdirectory.
-   We recommend that you create a PV for each application.
-   OSS is a FUSE file system that can be mounted based on the OSSFS plug-in. This method is suitable when you read configuration files, videos, and image files.
-   OSSFS is not suitable for data write scenarios. If you require write operations, we recommend that you use NAS file systems.
-   You can modify parameter configurations to optimize OSSFS performance in caching and permission management. For more information, see [t4872.md\#](/intl.en-US/Tools/ossfs/FAQ.md), [ossfs/README-CN.md](https://github.com/aliyun/ossfs/blob/master/README-CN.md), and [FAQ](https://github.com/aliyun/ossfs/wiki/FAQ).

