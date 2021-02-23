# Limits

This topic describes the limits that apply when you use Container Service for Kubernetes \(ACK\) clusters.

## Overview

-   After an ACK cluster is created, take note of the following limits:
    -   You cannot change the virtual private cloud \(VPC\).
    -   You cannot change the cluster type. For example, you cannot change a dedicated Kubernetes cluster to a managed Kubernetes cluster.
    -   You cannot change the network plug-in.
    -   You cannot change the volume plug-in.
-   You cannot migrate applications across namespaces.
-   Elastic Compute Service \(ECS\) instances support both the pay-as-you-go and subscription billing methods. You can change the billing method of ECS instances from pay-as-you-go to subscription in the ECS console. Other resources, such as Server Load Balancer \(SLB\) instances, support only the pay-as-you-go billing method.
-   When you create clusters, expand clusters, or perform auto scaling, you may fail to create nodes due to the insufficient resource quota or inventory. These operations are subject to resource quota limits and inventory availability.
-   If you want to create subscription instances when you create a cluster, instance creation may fail due to resource quota limits or inventory availability. After a subscription instance is created, you cannot release it before the subscription expires. To avoid these issues, we recommend that you create pay-as-you-go instances when you create a cluster. You can change the billing method from pay-as-you-go to subscription in the ECS console if necessary.
-   ECS instance specifications: more than 1 CPU core and larger than 2 GiB of memory.

## Quota limits on ACK

|Item|Quota for general users|Quota increase|
|----|-----------------------|--------------|
|Real-name verification|Real-name verification is required.|N/A|
|Create pay-as-you-go instances|None|None|
|Combined maximum number of dedicated Kubernetes clusters and managed Kubernetes clusters|Default: 50. Upper limit: 2,000.|[Navigate to the Quota Center page to submit a ticket](https://quotas.console.aliyun.com/products/csk/quotas). |
|Maximum number of master nodes and worker nodes in an ACK cluster|Default: 100. Upper limit: 5,000.|[Navigate to the Quota Center page to submit a ticket](https://quotas.console.aliyun.com/products/csk/quotas). |
|Maximum number of pods on a worker node|The maximum number is configurable when you create the cluster in the console. The upper limit is 256.|N/A|
|Total number of ASK clusters|2|[Navigate to the Quota Center page to submit a ticket](https://quotas.console.aliyun.com/products/csk/quotas). |

## Quota limits on underlying cloud resources

|Resource|Item|Quota for general users|Quota increase|
|--------|----|-----------------------|--------------|
|[Computing](/intl.en-US/Product Introduction/Limits.md)|Resource Orchestration Service \(ROS\) quota|Default: 100.|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
|vCPU quota of pay-as-you-go instances|500 CPU cores|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
|High-specification pay-as-you-go instances \(more than 16 CPU cores\)|Not available for purchase. Only pay-as-you-go instances with fewer than 16 CPU cores can be purchased.|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
|vCPU quota of preemptible instances|800 CPU cores|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
|Change from pay-as-you-go to subscription|Not supported by the following instance families: t1, s1, s2, s3, c1, c2, m1, m2, n1, n2, and e3.|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
|Auto Scaling \(ESS\)|The maximum number of ECS instances in a scaling group is 2,000.|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
|Operating system|Nodes that run the following operating systems can be added to an ACK cluster:-   Alibaba Cloud Linux 2
-   CentOS 7.x

**Note:** CentOS 8.x and later are not supported.

-   Windows Server 2009 and Windows Server 2009 Core.

|N/A|
|[Networking](/intl.en-US/Product Introduction/Limits/Limits.md)|Maximum number of custom route entries in a route table|48|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
|Maximum number of vSwitches in a VPC|24|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
|Maximum number of VPCs under an account|10|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
|Maximum number of private IP addresses in a VPC|65535|N/A|
|Maximum number of IP addresses that can be added to a basic security group|2000|N/A|
|Maximum number of elastic network interfaces \(ENIs\)|50000|N/A|
|Maximum number of elastic IP addresses \(EIPs\) under an account|20|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
|[Server Load Balancing \(SLB\)](/intl.en-US/User Guide/Limits/Limits.md)|Maximum number of SLB instances under an account|60|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
|Maximum number of backend servers that can be attached to an SLB instance|200|N/A|
|Maximum number of listeners that can be added to an SLB instance|50|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
|Maximum number of times that an ECS instance can be repeatedly added to SLB instances as a backend server|50|N/A|
|[Elastic Block Storage \(EBS\)](/intl.en-US/Product Introduction/Limits.md)|Maximum number of pay-as-you-go cloud disks in all regions under an account|The quota is five times as much as the number of ECS instances across all regions. You can create at least 10 pay-as-you-go cloud disks under each account.|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
|Maximum capacity of pay-as-you-go cloud disks that are used as data disks under an account|Subject to ECS resource usage, regions, and disk types. You can go to the Privileges & Quotas page in the ECS console to view details. For more information, see [View quotas \(old version\)]().|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |

## Regions where ACK is available

The following table describes the regions where ACK is available.

-   Asia Pacific

    |Region|City|Region ID|
    |------|----|---------|
    |China \(Beijing\)|Beijing|cn-beijing|
    |China \(Zhangjiakou\)|Zhangjiakou|cn-zhangjiakou|
    |China \(Hohhot\)|Hohhot|cn-huhehaote|
    |China \(Ulanqab\)|Ulanqab|cn-wulanchabu|
    |China \(Hangzhou\)|Hangzhou|cn-hangzhou|
    |China \(Shanghai\)|Shanghai|cn-shanghai|
    |China \(Shenzhen\)|Shenzhen|cn-shenzhen|
    |China \(Heyuan\)|Heyuan|cn-heyuan|
    |China \(Guangzhou\)|Guangzhou|guangzhou|
    |China \(Chengdu\)|Chengdu|cn-chengdu|
    |China \(Hong Kong\)|Hong Kong|cn-hongkong|
    |Japan \(Tokyo\)|Tokyo|ap-northeast-1|
    |Singapore \(Singapore\)|Singapore|ap-southeast-1|
    |Australia \(Sydney\)|Sydney|ap-southeast-2|
    |Malaysia \(Kuala Lumpur\)|Kuala Lumpur|ap-southeast-3|
    |Indonesia \(Jakarta\)|Jakarta|ap-southeast-5|

-   Europe & Americas

    |Region|City|Region ID|
    |------|----|---------|
    |US \(Silicon Valley\)|Silicon Valley|us-west-1|
    |US \(Virginia\)|Virginia|us-east-1|
    |UK \(London\)|London|eu-west-1|
    |Germany \(Frankfurt\)|Frankfurt|eu-central-1|

-   Middle East & India

    |Region|City|Region ID|
    |------|----|---------|
    |UAE \(Dubai\)|Dubai|me-east-1|
    |India \(Mumbai\)|Mumbai|ap-south-1|


