# Limits

This topic describes the limits that apply when you use Container Service for Kubernetes \(ACK\) clusters.

## Overview

Before you use ACK, take note of the following limits:

-   Before you create ACK clusters, you must pass real-name verification.
-   After an ACK cluster is created, you cannot perform the following operations:
    -   Change the virtual private cloud \(VPC\).
    -   Change the type of the cluster. For example, you cannot change a dedicated Kubernetes cluster to a managed Kubernetes cluster.
    -   Change the network plug-in.
    -   Change the volume plug-in.
    -   Migrate applications across namespaces.
-   The following limits apply to Elastic Compute Service \(ECS\) instances that run in ACK clusters:
    -   ECS instances support the pay-as-you-go and subscription billing methods. Other resources, such as Server Load Balancer \(SLB\) instances, support only the pay-as-you-go billing method. You can change the billing method from pay-as-you-go to subscription in the ECS console.
    -   When you create clusters, expand clusters, or perform auto scaling of clusters, you may fail to create nodes due to resource quota limits or inventory availability.
    -   If you want to create subscription ECS instances when you create a cluster, you may fail to create the ECS instances due to resource quota limits or inventory availability. After a subscription ECS instance is created, you cannot release the ECS instance before the subscription expires.

        **Note:** To avoid these issues, we recommend that you create pay-as-you-go ECS instances when you create a cluster. You can change the billing method from pay-as-you-go to subscription in the ECS console based on your requirements.

    -   Requirements for ECS specifications: You must select ECS instances with four or more vCPUs and 8 GiB or more of memory.

## Quota limits

|Cluster type|Maximum number of clusters within an Alibaba Cloud account|Maximum number of nodes in a cluster|Maximum number of pods on a node|Quota increase|
|------------|----------------------------------------------------------|------------------------------------|--------------------------------|--------------|
|Professional managed Kubernetes cluster|100|1000|256|[Navigate to the Quota Center page to submit a ticket](https://quotas.console.aliyun.com/products/csk/quotas). |
|Standard managed Kubernetes cluster|20|40|256|N/A|
|Dedicated Kubernetes cluster|20|40|256|N/A|
|Serverless Kubernetes \(ASK\) cluster|5|N/A|N/A|[Navigate to the Quota Center page to submit a ticket](https://quotas.console.aliyun.com/products/csk/quotas). |
|Edge Kubernetes cluster|20|100|256|[Navigate to the Quota Center page to submit a ticket](https://quotas.console.aliyun.com/products/csk/quotas). |
|Registered Kubernetes cluster|5|N/A|N/A|[Navigate to the Quota Center page to submit a ticket](https://quotas.console.aliyun.com/products/csk/quotas). |

**Note:** The maximum number of pods on a node cannot be increased. The quota increase methods apply to only the maximum number of clusters within an Alibaba Cloud account and the maximum number of nodes in a cluster.

## Quota limits on underlying cloud resources

|Category|Item|Quota for general users|Quota increase|
|--------|----|-----------------------|--------------|
|[Computing](/intl.en-US/Product Introduction/Limits.md)|Resource Orchestration Service \(ROS\) quota|Default: 100.|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
|vCPU quota of pay-as-you-go instances|500 CPU cores|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
|High-specification pay-as-you-go instances \(with more than 16 CPU cores\)|Unavailable for purchase. Only pay-as-you-go instances with less than 16 vCPUs can be purchased.|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
|vCPU quota of preemptible instances|800 CPU cores|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
|Change from pay-as-you-go to subscription|Not supported by the following instance families: t1, s1, s2, s3, c1, c2, m1, m2, n1, n2, and e3.|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
|Auto Scaling \(ESS\)|The maximum number of ECS instances in a scaling group is 2,000.|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
|Operating systems|Nodes that run the following operating systems can be added to an ACK cluster:-   Alibaba Cloud Linux 2
-   CentOS 7.x

**Note:** CentOS 8.x and later are not supported.

-   Windows Server 2009 and Windows Server 2009 Core.

|N/A|
|[Networking](/intl.en-US/Product Introduction/Limits/Limits.md)|Maximum number of custom route entries in a route table|48|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
|Maximum number of vSwitches in a VPC|24|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
|Maximum number of VPCs within an account|10|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
|Maximum number of private IP addresses in a VPC|65535|N/A|
|Maximum number of IP addresses that can be added to a basic security group|2000|N/A|
|Maximum number of elastic network interfaces \(ENIs\)|50000|N/A|
|Maximum number of elastic IP addresses \(EIPs\) within an account|20|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
|[SLB](/intl.en-US/Classic Load Balancer/User Guide/Limits/Limits.md)|Maximum number of SLB instances within an account|60|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
|Maximum number of backend servers that can be attached to an SLB instance|200|N/A|
|Maximum number of listeners that can be added to an SLB instance|50|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
|Maximum number of times that an ECS instance can be added to SLB instances as a backend server|50|N/A|
|[Elastic Block Storage \(EBS\)](/intl.en-US/Product Introduction/Limits.md)|Maximum number of pay-as-you-go disks in all regions within an account|The quota is five times the number of ECS instances across all regions within an account. You can create at least 10 pay-as-you-go disks within each account.|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
|Maximum capacity of pay-as-you-go disks that are used as data disks within an account|Subject to ECS resource usage, regions, and disk types. You can go to the Privileges & Quotas page in the ECS console to view details. For more information, see [View quotas \(old version\)]().|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |

