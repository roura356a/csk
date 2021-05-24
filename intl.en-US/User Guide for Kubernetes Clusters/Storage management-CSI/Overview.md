---
keyword: [container storage, CSI, storage architecture]
---

# Overview

Container Service for Kubernetes \(ACK\) provides container storage features based on the Kubernetes storage system. The storage features are integrated with Alibaba Cloud storage services and are fully compatible with native storage objects of Kubernetes, such as EmptyDir, HostPath, Secrets, and ConfigMaps. ACK provides the Container Storage Interface \(CSI\) plug-in based on the open source CSI. You must deploy the CSI plug-in before you can use Alibaba Cloud storage services in ACK clusters. This topic describes the overview, features, and limits of the CSI plug-in, and the authorizations that are required to use the CSI plug-in.

## Container storage architecture

![overview](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6968281261/p251924.png)

ACK allows you to configure storage services to be automatically mounted on pods. The storage services include Alibaba Cloud disks, Apsara File Storage NAS \(NAS\), Object Storage Service \(OSS\), Cloud Paralleled File System \(CPFS\), and local volumes. The following table describes the features and scenarios of the storage services.

|Alibaba Cloud storage service|Static volume|Dynamic volume|Deployed by default|Feature|Scenario|
|-----------------------------|-------------|--------------|-------------------|-------|--------|
|Disk|Supported|Supported|Yes|Non-shared storage. A disk can be mounted on only one node.|-   High I/O and low latency

Disks are block storage devices and are suitable for use in scenarios that require high I/O performance and low latency. For example, databases and middleware services.

-   Non-data sharing

A disk can be used to provision storage for only one pod. You can use disks as volumes in scenarios that do not require data sharing.


For more information, see [Use Alibaba Cloud disks that are mounted as PVs](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Disk volumes/Use Alibaba Cloud disks that are mounted as PVs.md). |
|NAS|Supported|Supported|Yes|Shared storage that provides high performance and high throughput.|-   Data sharing

NAS file systems allow multiple pods to access the same data. We recommend that you use NAS file systems if data needs to be shared.

-   Big data analysis

NAS file systems provide high throughput and meet the requirement of shared storage access when large numbers of jobs are involved.

-   Web applications

NAS file systems can provision storage for web applications and content management systems.

-   Log storage

We recommend that you use NAS file systems as volumes to store log data.


For more information, see [Use NAS file systems provided by Apsara File Storage NAS](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/NAS volumes/Use NAS file systems provided by Apsara File Storage NAS.md). |
|OSS|Supported|Not supported|Yes|Shared storage that supports file systems in the user space.|-   Read-only media files such as video and image files

You can use OSS buckets as volumes to read the preceding types of files.

-   Read-only configuration files of websites and applications

ossfs provides limited network performance and can be used to read small files.


**Note:** OSS buckets are mounted by using ossfs, which is implemented as a filesystem in the user space \(FUSE\). The write performance is limited when you use OSS buckets as volumes. We recommend that you use other storage media as volumes in scenarios that require high write performance.

For more information, see [Provision OSS buckets as PVs](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/OSS volumes/Provision OSS buckets as PVs.md). |
|Cloud Paralleled File System \(CPFS\)|Supported|Supported|No|Shared storage that features high performance and high bandwidth|-   Genomics computing and big data analysis

CPFS provides high throughput and meets the requirement of high performance in large-scale clusters.

-   Data cache access

You can download data from low-speed storage to CPFS volumes. This way, applications can access the data at a high speed.


For more information, see [Static PVs](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/CPFS volumes/Static volumes.md) and [Dynamic PVs](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/CPFS volumes/Dynamic volumes.md). |

**Note:** You can use the CSI plug-in to mount storage as static volumes and dynamic volumes in ACK clusters. To mount storage as a static volume, you must create a persistent volume \(PV\) and a persistent volume claim \(PVC\). When large numbers of PVs and PVCs are required, you can create a dynamic volume to mount storage. Definitions of PV and PVC:

-   **PV**

    A PV is a piece of storage in the cluster. A PV has a lifecycle that is independent of the pod that uses the PV. Different types of PV can be created based on different StorageClasses.


-   **PVC**

    A PVC is a request for storage in the cluster. PVs are node resources consumed by pods. PVCs are claims that consume PV resources. When PVs are insufficient, PVCs can dynamically provision PVs.


## Container storage features

The following table describes the storage features supported by different types of ACK cluster.

|Storage type|Feature|ACK cluster \(Linux-based\)|ASK cluster|Registered cluster \(hybrid cloud or multicloud\)|ACK@Edge|ACK cluster \(Windows-based\)|Dedicated ACK cluster|Sandboxed container|
|------------|-------|---------------------------|-----------|-------------------------------------------------|--------|-----------------------------|---------------------|-------------------|
|Block storage|Mount and unmount disks|✔️|✔️|❌|❌|✔️|✔️|✔️|
|Resize|✔️|❌|❌|❌|❌|✔️|❌|
|Snapshots|✔️|✔️|❌|❌|❌|✔️|✔️|
|Container I/O monitoring|✔️|❌|❌|❌|❌|✔️|❌|
|File systems|XFS, ext4, and dBFS are supported.|XFS and ext4 are supported.|❌|❌|NTFS is supported.|XFS and ext4 are supported.|XFS and ext4 are supported.|
|Block and bare devices|✔️|❌|❌|❌|❌|✔️|❌|
|Data restoration from snapshots|✔️|✔️|❌|❌|❌|✔️|✔️|
|Disk queue settings|✔️|❌|❌|❌|❌|✔️|❌|
|CMK and BYOK|✔️|✔️|❌|❌|✔️|✔️|✔️|
|Multi-zone|✔️|❌|❌|❌|✔️|✔️|✔️|
|Custom labels|✔️|✔️|❌|❌|✔️|✔️|✔️|
|Cross-host migration|✔️|✔️|❌|❌|✔️|✔️|✔️|
|File storage|Create, mount, and unmount NAS file systems|✔️|✔️|✔️|✔️|✔️|✔️|✔️|
|Mount and unmount Samba file systems|❌|❌|✔️|❌|✔️|❌|❌|
|CPFS|✔️|❌|✔️|❌|❌|❌|❌|
|NAS recycle bin|❌|❌|❌|❌|❌|❌|❌|
|Subdirectories of dynamic volumes|✔️|❌|✔️|✔️|✔️|✔️|✔️|
|CMK \(Extreme NAS\)|✔️|❌|❌|❌|❌|✔️|✔️|
|Object storage|Mount and unmount OSS buckets|✔️|✔️|✔️|✔️|❌|✔️|✔️|
|BYOK|✔️|✔️|✔️|✔️|❌|✔️|✔️|
|Local storage|LVM-managed block storage|✔️|❌|✔️|✔️|❌|✔️|❌|
|Automated volume groups|✔️|❌|✔️|✔️|❌|✔️|❌|
|Node capacity scheduling|✔️|❌|✔️|✔️|❌|✔️|❌|
|PMEM Direct Mem|✔️|❌|❌|❌|❌|❌|❌|
|LVM-managed PMEM|✔️|❌|❌|❌|❌|❌|❌|

## CSI deployment architectures

The CSI plug-in consists of two parts: CSI-Plugin and CSI-Provisioner. The following figure shows the deployment architectures of the CSI plug-in in a managed Kubernetes cluster and a dedicated Kubernetes cluster.

**Note:** By default, the CSI plug-in is installed in managed and dedicated Kubernetes clusters. If you use a serverless Kubernetes cluster or an edge Kubernetes cluster, you must manually install the CSI plug-in.

|Managed Kubernetes cluster|Dedicated Kubernetes cluster|
|--------------------------|----------------------------|
|![csi managed](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5133941261/p249741.png)

In managed Kubernetes clusters, CSI-Provisioner and CSI-Plugin are deployed on worker nodes.

|![flexvolume](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5133941261/p249742.png)

In dedicated Kubernetes clusters, CSI-Provisioner is deployed on master nodes. CSI-Plugin is automatically deployed as DaemonSets on master and worker nodes. |

## Permissions required to use CSI

Before you can use the CSI plug-in to mount, unmount, create, and delete volumes, you must grant the plug-in the permissions to access other cloud resources. You can use an AccessKey pair or a Resource Access Management \(RAM\) role to grant permissions to the CSI plug-in. The default method is to use a RAM role. The following table describes the two authorization methods.

|Use an AccessKey pair|Use a RAM role|
|---------------------|--------------|
|-   You can specify an AccessKey pair in the deployment template of the CSI plug-in.
-   You can also create a Secret to pass an AccessKey pair as environment variables.

|The CSI plug-in uses the RAM role **AliyunCSManagedCsiRole** to access resources of other cloud services. For more information, see [AliyunCSManagedCsiRole](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/ACK default roles.md). For more information about how to grant permissions to a RAM role, see [Grant permissions to a RAM role](/intl.en-US/RAM Role Management/Grant permissions to a RAM role.md).

-   Managed clusters

The permission token of the RAM role used by the CSI plug-in is stored in a Secret named **addon.csi.token**. To grant permissions to the CSI plug-in and allow the plug-in to call API operations, you need only to mount the Secret on the plug-in.

-   Dedicated clusters

The CSI plug-in uses the RAM role of the Elastic Compute Service \(ECS\) node that hosts the pod of the plug-in. |

## CSI limits

When you use the CSI plug-in in ACK clusters, take note of the limits of the CSI plug-in and Alibaba Cloud storage services.

-   **Limits of Alibaba Cloud storage services**

    |Alibaba Cloud storage service|Limits|
    |-----------------------------|------|
    |Disk|    -   You can mount up to 15 disks as volumes on a node.
    -   You can provision a disk to only one pod as a volume.
    -   You cannot mount disks of all types on all ECS instances. For more information, see [Instance families](/intl.en-US/Instance/Instance families.md).
    -   You cannot mount or unmount subscription disks as volumes.
    -   You can mount a disk only on an ECS instance that is in the same zone as the disk.
    -   We recommend that you create StatefulSets instead of Deployments to use volumes that are created from disks.

**Note:** Deployments are used to create stateless applications. When a pod is restarted, the start time of the new pod may overlap the end time of the old pod. If multiple pods are created for a Deployment, no dedicated volume is provisioned for each pod.

    -   The minimum capacity of each volume is 20 GiB. |
    |NAS|    -   You can mount a NAS file system only on an ECS instance that is deployed in the same virtual private cloud \(VPC\) as the NAS file system.
    -   The number of NAS file systems that you can create is subject to a quota limit. To request a quota increase, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to the NAS team. |
    |OSS|We recommend that you do not perform data write operations on volumes that are created from OSS buckets. Use other storage media for data write operations. **Note:** OSS buckets are mounted by using ossfs, which is implemented as a filesystem in the user space \(FUSE\). The write performance is limited when you use OSS buckets as volumes. We recommend that you use other storage media in scenarios that require high write performance. |
    |CPFS|    -   The CPFS driver is highly dependent on the OS kernel. After you deploy the CPFS environment, do not upgrade the OS kernel.
    -   You can install the CPFS driver but cannot upgrade the CPFS driver.
**Note:**

The CSI-CPFS plug-in is a Kubernetes CSI component that mounts CPFS file systems as volumes on pods for the use of applications.

The CPFS driver is a client driver that implements the CPFS protocol at the kernel layer.

Relationship between the CSI-CPFS plug-in and the CPFS driver:

    -   If the CPFS driver is not installed on a node, the CPFS driver is automatically installed when you deploy the CSI-CPFS plug-in.
    -   If the CPFS driver is installed on a node, the CPFS driver is not installed or upgraded when you deploy the CSI-CPFS plug-in. |

-   **Limits of the CSI plug-in**

    The CSI plug-in is an open source plug-in for ACK clusters. In other types of clusters, such as clusters deployed in third-party clouds and self-managed clusters on Alibaba Cloud, you cannot directly use the CSI plug-in for reasons such as cluster configurations, permission management, and network differences. If you want to use the CSI plug-in in these types of clusters, you must modify the cluster configurations based on the source code. For more information, see [alibaba-cloud-csi-driver](https://github.com/kubernetes-sigs/alibaba-cloud-csi-driver/tree/master).


## Kubernetes version requirements

To use the CSI plug-in in an ACK cluster, the Kubernetes version of the cluster must be 1.14 or later. Besides, the kubelet parameter `--enable-controller-attach-detach` must be set to `true`.

## Differences between the CSI and FlexVolume plug-ins

|Plug-in|Feature|References|
|-------|-------|----------|
|Flexvolume|FlexVolume is a traditional mechanism to extend Kubernetes storage systems developed by the Kubernetes community. ACK supports FlexVolume. FlexVolume consists of the following parts: -   FlexVolume: allows you to mount and unmount volumes. By default, ACK allows you to mount the following types of storage media: disks, NAS file systems, and OSS buckets.
-   Disk-Controller: automatically mounts disks as volumes.
-   Nas-Controller: automatically mounts NAS file systems as volumes.

|For more information about FlexVolume, see [Overview](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Overview.md).

For more information about how to upgrade FlexVolume, see [Manage system components](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Upgrade cluster/Manage system components.md). |
|CSI|The Kubernetes community recommends the CSI plug-in. The CSI plug-in provided by ACK is compatible with the features of the community version. CSI consists of the following two parts: -   CSI-Plugin: allows you to mount and unmount volumes. By default, ACK allows you to mount the following types of storage media: disks, NAS file systems, and OSS buckets.
-   CSI-Provisioner: automatically mounts disks and NAS file systems as volumes.

|For more information about CSI, see [Overview](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Overview.md) and [alibaba-cloud-csi-driver](https://github.com/kubernetes-sigs/alibaba-cloud-csi-driver). |

**Note:**

-   You must select a plug-in when you create an ACK cluster.
-   You cannot use CSI and FlexVolume in the same cluster.
-   You cannot change the plug-in from FlexVolume to CSI for a cluster.

**Recommendation**

-   For new ACK clusters, we recommend that you use CSI. The ACK technical team will continuously upgrade CSI to support more features of the CSI community version.
-   For existing clusters, we recommend that you use the plug-in that is already installed. The ACK technical team will continue its support for FlexVolume.

**Related topics**  


[Use Alibaba Cloud disks that are mounted as PVs](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Disk volumes/Use Alibaba Cloud disks that are mounted as PVs.md)

[Use NAS file systems provided by Apsara File Storage NAS](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/NAS volumes/Use NAS file systems provided by Apsara File Storage NAS.md)

[Provision OSS buckets as PVs](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/OSS volumes/Provision OSS buckets as PVs.md)

[Static PVs](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/CPFS volumes/Static volumes.md)

[Dynamic PVs](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/CPFS volumes/Dynamic volumes.md)

