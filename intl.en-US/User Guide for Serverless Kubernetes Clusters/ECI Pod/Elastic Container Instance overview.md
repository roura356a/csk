---
keyword: [Elastic Container Instance, limits on Elastic Container Instance, Elastic Container Instance pricing]
---

# Elastic Container Instance overview

This topic describes the configurations of an Elastic Container Instance-based pod and the limits on Elastic Container Instance. You can customize the following configurations for an elastic container instance-based pod: security isolation, CPU and memory resources and specifications, image pulling, storage, network modes, and log collection.

-   A virtual node is deployed in a Container Service for Kubernetes \(ACK\) cluster or a serverless Kubernetes \(ASK\) cluster is created. For more information, see [Step 1: Deploy ack-virtual-node in ACK clusters](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Deploy the virtual node controller and use it to create Elastic Container Instance-based pods.md) and [Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md).
-   Elastic Container Instance is activated. You can log on to the [Elastic Container Instance console](https://eci.console.aliyun.com/) to activate the service.

## Isolation

Elastic Container Instance provides a secure and reliable runtime environment for containers of serverless applications. Elastic container instances are completely isolated from one another because sandboxed containers run on lightweight virtual machines. Elastic container instances can be scheduled to run on different physical machines. This ensures high availability.

## CPU and memory resources and specifications

Resources are applied and charged in the following ways:

-   **Specify CPU and memory resources**

    -   **Specify CPU and memory resources for containers**: In the ACK console, you can specify CPU and memory resources for a container by setting the `resources and limits` parameters. If you do not set the parameters, 1 CPU core and 2 GB of memory are allocated to each container. The amount of resources that are required by an elastic container instance refers to the total amount of resources that are required by all containers in the elastic container instance.If the specified specification is not supported, Elastic Container Instance automatically adjusts the CPU and memory resources as required. For example, if 2 CPU cores and 3 GB of memory are specified for all containers, the allocated resources are adjusted to 2 CPU cores and 4 GB of memory. However, if more than four CPU cores are specified for all containers, the allocation is not adjusted.
    -   **Specify CPU and memory resources for elastic container instances**: You can specify CPU and memory resources for an elastic container instance by adding annotations. In this case, the CPU and memory resources required by an elastic container instance are not equal to the total amount of resources that are required by all containers. The elastic container instance is created and billed based on the specified resources. After you specify CPU and memory resources for an elastic container instance, you do not need to set the `resource request` and `resource limit` of each container. Containers in the elastic container instance can use computing resources to the maximum extent.
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

    **Calculation formula**: Elastic Container Instance fee = \(Number of CPU cores × Price per CPU core + Memory size × Price per GB\) × Elastic Container Instance running duration. Elastic Container Instance is billed on a per-second basis. The following table describes the unit price of each billable item.

    |Item|Unit Price|Unit price per hour|
    |----|----------|-------------------|
    |CPU \(per vCPU per second\)|USD 0.0000077|USD 0.02772 per hour|
    |Memory \(per GB per second\)|USD 0.00000096|USD 0.003456 per hour|

-   **Specify ECS instance types for pods**

    You can specify Elastic Compute Service \(ECS\) instance types to create elastic container instances with specific capabilities. For example, you can use instance family [ecs.sn1ne](/intl.en-US/Instance/Instance families.md) to create an elastic container instance with enhanced network performance. For more information, see [ECS instance families](/intl.en-US/Instance/Instance families.md) and [Pay-as-you-go ECS pricing based on regions](https://www.alibabacloud.com/zh/pricing).

    **Note:** When you specify an ECS instance type to create an elastic container instance, the computing resources consumed by the elastic container instance are billed at the rate of the specified ECS instance type. The following ECS instance families are supported:

    -   General purpose instance families: g6e, g6, g5, and sn2ne
    -   Compute optimized instance families: c6e, c6a, c6, c5, and sn1ne
    -   Memory optimized instance families: r6e, r6, r5, se1ne, and se1
    -   Compute intensive instance family: ic5
    -   Compute optimized instance families with high clock speeds: hfc6 and hfc5
    -   General purpose instance families with high clock speeds: hfg6 and hfg5
    -   GPU-accelerated compute optimized instance families: gn6i, gn6v, gn5i, and gn5
    -   Big data instance family with enhanced network performance: d1ne
    -   Instance families with local SSDs: i2 and i2g
    -   Burstable instance families: t6 and t5
    -   Shared performance instance families: s6, xn4, n4, mn4, and e4
    **Calculation formula**: Elastic Container Instance fee =[Unit price of ECS specification](https://www.alibabacloud.com/zh/pricing) specified by ECS × Elastic Container Instance running duration. Elastic Container Instance is billed on a per-second basis.

    When you specify ECS instance types for elastic container instances, you can use reserved instances to deduct the Elastic Container Instance cost. For more information, see [Reserved instance overview](/intl.en-US/Instance/Instance purchasing options/Reserved Instances/Overview.md).

    The Elastic Container Instance costs after the reserved instances are applied are close to the costs of subscription ECS instances.

-   **Preemptible instance**

    You can use preemptible instances by adding annotations. Using preemptible instances can significantly reduce the computing costs.


## Image pulling

When an Elastic Container Instance starts, the containerd runtime of the elastic container instance pulls container images from a remote image repository. To allow an elastic container instance to pull public images, you must configure a Network Address Translation \(NAT\) gateway for the virtual private cloud \(VPC\) where the elastic container instance is deployed. You can also associate an elastic IP address \(EIP\) with the elastic container instance to pull public images. We recommend that you store container images in Container Registry to reduce the time that is required to pull images through a VPC. In addition, You can pull private images from Container Registry without a password. This ensures high efficiency for image pulling.

Elastic Container Instance supports image snapshots. Elastic container instances cache the pulled container images as snapshots, and then use these snapshots to launch containers. This prevents repetitive pulling requests for the same image from the remote image repository, which saves time and costs. This feature is suitable for the use of large images.

## Storage methods

Elastic Container Instance supports the following storage methods:

-   FlexVolume:
    -   You can set the `disk volume Id` parameter to mount a disk volume by using the same method that is used for FlexVolume.
    -   You can set the `disk volume Id` parameter to mount a disk volume by using the same method that is used for FlexVolume.
    -   You can use FlexVolume to dynamically create `disk volumes` when you create an elastic container instance. You can also specify the size of the `disk volume` and whether to retain the `disk volume` when the elastic container instance stops running.
-   Network File System \(NFS\): For more information, see [Example](https://github.com/AliyunContainerService/serverless-k8s-examples/blob/master/nas-volume/nas-volume.yaml).
-   Persistent volumes \(PVs\) and persistent volume claims \(PVCs\): For more information, see [Example](https://github.com/AliyunContainerService/serverless-k8s-examples/tree/master/pvc).

## Network mode

By default, Elastic Container Instance-based pods use the host network mode. Each pod is assigned an elastic network interface \(ENI\) by a vSwitch.

In a Kubernetes cluster, you can use the following methods to enable an Elastic Container Instance-based pod to communicate with pods that run on ECS instance-based nodes.

-   Attach Elastic Container Instance-based pods to LoadBalancer type Services: You can attach both Elastic Container Instance-based pods and pods that run on ECS instances-based nodes to LoadBalancer type Services.
-   Access ClusterIP type Services: An Elastic Container Instance-based pod can access the IP address of a cluster.
-   Associate EIPs with Elastic Container Instance-based pods: You can associate EIPs with Elastic Container Instance-based pods. You can enable an Elastic Container Instance-based pod to automatically create an EIP or associate an existing EIP with the Elastic Container Instance-based pod.

## Log collection

You can set environment variables of an elastic container instance to collect `stdout` files or log files and import them to Log Service. In most cases, you do not need to deploy a `sidecar` container that works as Logtail.

## List of supported annotations

**Note:** You must add annotations in the pod configurations. Do not add annotations in the Deployment configurations.

|Annotation|Description|Example|
|----------|-----------|-------|
|k8s.aliyun.com/eci-use-specs|Specifies the instance types and specifications that can be used. You can specify multiple instance types. If a specified instance type is out of stock, the system selects another instance type to create instances. You can use the $\{cpu\}-$\{mem\}Gi format or specify instance types.|"k8s.aliyun.com/eci-use-specs": "2-4Gi,4-8Gi,ecs.c6.xlarge"|
|k8s.aliyun.com/eci-vswitch|Sets a vSwitch for an elastic container instance.|"k8s.aliyun.com/eci-vswitch" : "$\{your\_vsw\_id\}"|
|k8s.aliyun.com/eci-security-group|Sets a security group for an elastic container instance.|"k8s.aliyun.com/eci-security-group" : "$\{your\_security\_group\_id\}"|
|k8s.aliyun.com/eci-resource-group-id|Sets a resource group to which an elastic container instance belongs.|"k8s.aliyun.com/eci-resource-group-id" : "$\{your\_resource\_group\_id\}"|
|k8s.aliyun.com/eci-ram-role-name|Sets the Resource Access Management \(RAM\) role for an elastic container instance to allow it to access other Alibaba Cloud services.|"k8s.aliyun.com/eci-ram-role-name" : "$\{your\_ram\_role\_name\}"|
|k8s.aliyun.com/eci-image-snapshot-id|Specifies the ID of a cached image to accelerate elastic container instance creation.|k8s.aliyun.com/eci-image-snapshot-id: "$\{your\_image\_cache\_id\}"|
|k8s.aliyun.com/eci-image-cache|Automatically matches existing cached images. The default value: false.|k8s.aliyun.com/eci-image-cache: "true"|
|k8s.aliyun.com/eci-with-eip|Creates an EIP and associates it with an elastic container instance.|"k8s.aliyun.com/eci-with-eip": "true"|
|k8s.aliyun.com/eip-bandwidth|Sets the bandwidth for an EIP. The default bandwidth is 5 Mbit/s.|"k8s.aliyun.com/eci-with-eip": "true""k8s.aliyun.com/eip-bandwidth": 10|
|k8s.aliyun.com/eci-eip-instanceid|Associates an existing EIP with an elastic container instance.|"k8s.aliyun.com/eci-eip-instanceid": "$\{your\_eip\_Instance\_Id\}"|
|k8s.aliyun.com/eci-spot-strategy|If you set this parameter to SpotAsPriceGo, the system automatically bids based on the current market price.

SpotWithPriceLimit: specifies the highest bid for the preemptible instance.

|k8s.aliyun.com/eci-spot-strategy: "SpotAsPriceGo"|
|k8s.aliyun.com/eci-spot-price-limit|Valid only when k8s.aliyun.com/eci-spot-strategy is set to SpotWithPriceLimit. Sets the highest price within one hour. The price can be accurate to three decimal places.|k8s.aliyun.com/eci-spot-price-limit: "0.250"|
|k8s.aliyun.com/eci-ntp-server|Sets one or more Network Time Protocol \(NTP\) servers.|k8s.aliyun.com/eci-ntp-server: 100.100.5.1,100.100.5.2 \# The IP addresses of the NTP servers.|
|k8s.aliyun.com/eci-set-diskvolume|Converts the volume to a dynamic disk. For example, emptyDir and hostPath volumes can be converted to dynamic disks. The format is $volumeName:$type:$size.|k8s.aliyun.com/eci-set-diskvolume: "cache-volume:ext4:500Gi"|

## Limits on Elastic Container Instance

Elastic container instances and virtual nodes support most pod features. However, Elastic container instance do not support the following features:

-   Run DaemonSet pods on virtual nodes.
-   Set the hostPath and hostPID parameters.
-   Add privileged permissions.
-   Create NodePort type Services.
-   Set network policies.

