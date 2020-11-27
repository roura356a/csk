# Volumes and environment variables

This topic describes the differences in volume and environment variable configurations when you create an application from an image in a cluster of Container Service for Swarm and in a cluster of Container Service for Kubernetes \(ACK\).

## Create an application from an image

The interfaces for creating an application from an image in a Swarm cluster and in an ACK cluster are quite different.

-   For more information about how to create an application from an image in a Swarm cluster, see [Create an application](/intl.en-US/User Guide/Applications/Create an application.md).
-   For more information about how to create an application from an image in an ACK cluster, see [Use an image to create a stateless application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use an image to create a stateless application.md).

## Volume

Swarm cluster

You need to specify your cloud or local storage path.

![Swarm cluster](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5546858951/p35444.png)

ACK cluster

You can configure storage devices in ACK clusters in the same way as in Swarm clusters, except that ACK clusters adopt a different method to mount storage devices.

![ACK cluster](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6546858951/p35447.png)

You can use local storage or cloud storage.

-   Local storage includes host directories, ConfigMaps, Secrets, and temporary directories.
-   Cloud storage includes cloud disks, Network Attached Storage \(NAS\) file systems, and Object Storage Service \(OSS\) buckets.

## Environment variable

You can configure environment variables in ACK clusters in the same way as in Swarm clusters. You only need to enter a key and a value to define an environment variable.

![Environment variable](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6546858951/p35452.png)

