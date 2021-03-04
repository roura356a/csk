# Use an OSS volume

You can use Object Storage Service \(OSS\) buckets to create PVs in Container Service for Kubernetes \(ACK\) clusters.

Only statically provisioned OSS volumes are supported. You can use OSS volumes in the following ways:

-   Use an OSS bucket as a volume
-   Use an OSS bucket to create a PV and a PVC

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

## Notes

-   If your ACK cluster had been created before February 6, 2018, you must install required plug-ins before you use OSS volumes. For more information, see [t15789.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Install plug-ins.md). To use OSS volumes, when you deploy the FlexVolume service, you must create a Secret and enter your AccessKey information in the Secret.
-   When you upgrade an ACK cluster or restart the kubelet, the OSSFS plug-in restarts. In this case, OSS volumes that are mounted based on an earlier FlexVolume version must be mounted again, and the pods that use the OSS volumes must be recreated. You can upgrade FlexVolume to the latest version to fix this issue.

