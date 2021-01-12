---
keyword: [ECI, ECI limits, ECI pricing]
---

# ECI overview

This topic describes the configurations and limits of an elastic container instance \(ECI\). You can customize the following configurations for an ECI: security isolation, CPU and memory resources and specifications, image pulling, storage, network modes, and log collection.

-   A virtual node is deployed in a cluster of Container Service for Kubernetes \(ACK\) or a serverless Kubernetes \(ASK\) cluster is created. For more information, see [Deploy ack-virtual-node in an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Deploy ack-virtual-node in an ACK cluster.md) and [Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md).
-   ECI is activated. Log on to the [ECI console](https://eci.console.aliyun.com/) to activate ECI.

## Secure isolation

ECI provides a secure and reliable runtime environment for serverless containers. ECIs are completely isolated from one aother because sandboxed containers run on lightweight virtual machines. ECIs can be scheduled to run on different physical machines. This ensures high availability.

## CPU and memory resources and specifications

Resources are applied and charged in the following ways:

-   **Specify CPU and memory resources**

    -   **Specify CPU and memory resources for containers**: In the ACK console, you can specify CPU and memory resources for a container by setting the resources and limits parameters. If you do not set the parameters, 1 CPU core and 2 GB of memory are allocated to each container. The amount of resources that are required by an ECI refers to the total amount of resources that are required by all containers in the ECI. If the specified specification is not supported, ECI automatically adjusts the CPU and memory resources as required. For example, if 2 CPU cores and 3 GB of memory are specified for all containers, the allocated resources are adjusted to 2 CPU cores and 4 GB of memory. However, if more than four CPU cores are specified for all containers, the allocation is not adjusted.
    -   **Specify CPU and memory resources for ECIs**: You can specify CPU and memory resources for an ECI by adding annotations. In this case, the CPU and memory resources required by an ECI are not equal to the total amount of resources that are required by all containers. The ECI is created and billed based on the specified resources. After you specify CPU and memory resources for an ECI, you do not need to set the resource request and resource limit for each container. Containers in the ECI can use computing resources to the maximum extent.
    The following table describes the supported CPU and memory specifications.

    |vCPU|Memory|
    |----|------|
    |.25 vCPU|0.5 GB, 1 GB|
    |.5 vCPU|1 GB, 2 GB|
    |1 vCPU|2 GB, 4 GB, 8 GB|
    |2 vCPU|2 GB, 4 GB, 8 GB, 16 GB|
    |4 vCPU|4 GB, 8 GB, 16 GB, 32 GB|
    |8 vCPU|8 GB, 16 GB, 32 GB, 64 GB|
    |12 vCPU|12 GB, 24 GB, 48 GB, 96 GB|
    |16 vCPU|16 GB, 32 GB, 64 GB, 128 GB|
    |24 vCPU|48 GB, 96 GB, 192 GB|
    |32 vCPU|64 GB, 128 GB, 256 GB|
    |52 vCPU|96 GB, 192 GB, 384 GB|
    |64 vCPU|128 GB, 256 GB, 512 GB|

    The billing period of CPU and memory resources starts when external storage is mounted to an ECI or container images are downloaded. The billing period ends when the ECI stops running \(in the Succeeded or Failed state\).

    **Calculation formula**: ECI fee = \(Number of CPU cores × Price per CPU core + Memory size × Price per GB\) × ECI running duration. ECI is billed on a per-second basis. The following table describes the unit price of each billable item.

    |Item|Unit price|Unit price per hour|
    |----|----------|-------------------|
    |CPU \(per vCPU per second\)|USD 0.0000077|USD 0.02772 per hour|
    |Memory \(per GB per second\)|USD 0.00000096|USD 0.003456 per hour|

-   **Specify ECS instance types for pods**

    ECI supports Elastic Compute Service \(ECS\) instances of [ECS instance families](/intl.en-US/Instance/Instance families.md). For more information about the ECS pricing,see [Pay-as-you-go ECS pricing based on regions](https://www.alibabacloud.com/zh/pricing). You can specify ECS instance types to create ECIs with specific capabilities. For example, you can use instance family [ecs.sn1ne](/intl.en-US/Instance/Instance families.md) to create an ECI with enhanced network performance.

    **Calculation formula**: ECI fee = [Unit price of ECS specification](https://www.alibabacloud.com/zh/pricing) specified by ECS × ECI running duration. ECI is billed on a per-second basis.

    When you specify ECS instance types for ECIs, you can use reserved instances to deduct the ECI cost. For more information, see [Reserved instance overview](/intl.en-US/Instance/Instance purchasing options/Reserved Instances/Reserved instance overview.md).

    The ECI costs after the reserved instances are applied are close to the costs of subscription ECS instances.

-   **Preemptible instance**

    You can use preemptible instances by adding annotations. Using preemptible instances can significantly reduce the computing costs.


## Image pulling

When an ECI starts, the containerd runtime of the ECI pulls container images from a remote image repository. To allow an ECI to pull public images, you must configure a Network Address Translation \(NAT\) gateway for the virtual private cloud \(VPC\) where the ECI is deployed. You can also associate an elastic IP address \(EIP\) with the ECI to pull public images. We recommend that you store container images in Container Registry to reduce the time that is required to pull images through a VPC. In addition, You can pull private images from Container Registry without a password. This ensures high efficiency for image pulling.

ECI supports image snapshots. ECIs cache the pulled container images as snapshots, and then use these snapshots to quickly launch containers. This prevents multiple pulling requests for the same image from the remote image repository, which saves time and costs. This feature is suitable for the use of large images.

## Storage methods

ECI supports the following storage methods:

-   FlexVolume:
    -   Mount a Network Attached Storage \(NAS\) volume: You can set the nas volume Id parameter to mount a NAS volume by using the same method that is used for FlexVolume.
    -   You can set the disk volume Id parameter to mount a disk volume by using the same method that is used for FlexVolume.
    -   You can use FlexVolume to dynamically create disk volumes when you create an ECI. You can also specify the size of the disk volume and whether to retain the disk volume when the ECI stops running.
-   Network File System \(NFS\): For more information, see [Example](https://github.com/AliyunContainerService/serverless-k8s-examples/blob/master/nas-volume/nas-volume.yaml).
-   Persistent volumes \(PVs\) through persistent volume claims \(PVCs\): For more information, see [Example](https://github.com/AliyunContainerService/serverless-k8s-examples/tree/master/pvc).

## Network

By default, ECI pods use the host network mode. Each pod is assigned an elastic network interface \(ENI\) by a vSwitch.

In a Kubernetes cluster, you can use the following methods to enable an ECI pod to communicate with pods that run on ECS instances-based nodes.

-   Attach ECI pods to LoadBalancer type Services: You can attach both ECI pods and pods that run on ECS instances-based nodes to LoadBalancer type Services.
-   Access ClusterIP type Services: An ECI pod can access the IP address of a cluster.
-   Associate EIPs with ECI pods: You can associate EIPs with ECI pods. You can enable an ECI pod to automatically create an EIP or associate an existing EIP with the ECI pod.

## Log collection

You can set environment variables of an ECI to collect stdout files or log files and import them to Log Service. In most cases, you do not need to deploy a sidecar container that works as Logtail.

## List of supported annotations

**Note:** You must add annotations in the Pod Spec parameter. Do not add annotations in the Deployment Spec parameter.

|Annotation|Description|Example|
|----------|-----------|-------|
|k8s.aliyun.com/eci-use-specs|Specifies the instance types and specifications that can be used. You can specify multiple instance types. If the stock of a specified instance type is insufficient, the system selects another instance type to create instances. The instance type can be set in the CPU-MEM format \($\{cpu\}-$\{mem\}Gi\), ECS instance type format, or GPU type format \(eci-gpu-$gputype-$gpucount\).|"k8s.aliyun.com/eci-use-specs": "2-4Gi,4-8Gi,ecs.c6.xlarge,ecigpu-P100-4"|
|k8s.aliyun.com/eci-vswitch|Sets a vSwitch for an ECI.|"k8s.aliyun.com/eci-vswitch" : "$\{your\_vsw\_id\}"|
|k8s.aliyun.com/eci-security-group|Sets a security group for an ECI.|"k8s.aliyun.com/eci-security-group" : "$\{your\_security\_group\_id\}"|
|k8s.aliyun.com/eci-resource-group-id|Sets a resource group to which an ECI belongs.|"k8s.aliyun.com/eci-resource-group-id" : "$\{your\_resource\_group\_id\}"|
|k8s.aliyun.com/eci-ram-role-name|Sets the Resource Access Management \(RAM\) role for an ECI to allow it to access other Alibaba Cloud services.|"k8s.aliyun.com/eci-ram-role-name" : "$\{your\_ram\_role\_name\}"|
|k8s.aliyun.com/eci-image-snapshot-id|Specifies the ID of a cached image to accelerate ECI creation.|k8s.aliyun.com/eci-image-snapshot-id: "$\{your\_image\_cache\_id\}"|
|k8s.aliyun.com/eci-image-cache|Automatically matches existing cached images. Default value: false.|k8s.aliyun.com/eci-image-cache: "true"|
|k8s.aliyun.com/eci-with-eip|Creates an EIP and associates it with an ECI.|"k8s.aliyun.com/eci-with-eip": "true"|
|k8s.aliyun.com/eip-bandwidth|Sets the bandwidth for an EIP. The default bandwidth is 5 Mbit/s.|"k8s.aliyun.com/eci-with-eip": "true""k8s.aliyun.com/eip-bandwidth": 10|
|k8s.aliyun.com/eci-eip-instanceid|Associates an existing EIP with an ECI.|"k8s.aliyun.com/eci-eip-instanceid": "$\{your\_eip\_Instance\_Id\}"|
|k8s.aliyun.com/eci-spot-strategy|If you set this parameter to SpotAsPriceGo, the system automatically bids based on the current market price.

If you set this parameter to SpotWithPriceLimit, the system sets the highest price of the preemptible instance.

|k8s.aliyun.com/eci-spot-strategy: "SpotAsPriceGo"|
|k8s.aliyun.com/eci-spot-price-limit|Valid only when k8s.aliyun.com/eci-spot-strategy is set to SpotWithPriceLimit. Sets the highest price within one hour. The price can be accurate to three decimal places.|k8s.aliyun.com/eci-spot-price-limit: "0.250"|
|k8s.aliyun.com/eci-ntp-server|Sets one or more Network Time Protocol \(NTP\) servers.|k8s.aliyun.com/eci-ntp-server: 100.100.5.1,100.100.5.2 \# Sets the IP addresses of your NTP servers.|
|k8s.aliyun.com/eci-set-diskvolume|Converts the volume to a dynamic disk. For example, emptyDir and hostPath volumes can be converted to dynamic disks. The format is $volumeName:$type:$size.|k8s.aliyun.com/eci-set-diskvolume: "cache-volume:ext4:500Gi"|

## Limits on ECI

ECI and virtual nodes support most pod features. However, ECI does not support the following features:

-   Run DaemonSet pods on virtual nodes.
-   Set the hostPath and hostPID parameters.
-   Add privileged permissions.
-   Create oversized ConfigMaps or mount oversized Secret volumes.
-   Create NodePort type Services.
-   Set network policies.

