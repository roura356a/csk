# Limits

This topic describes the limits that apply when you use Container Service for Kubernetes \(ACK\) clusters.

## Overview

Before you use ACK, take note of the following limits:

-   You must pass real-name verification before you can create an ACK cluster.
-   You cannot perform the following operations on ACK clusters after the clusters are created:
    -   Change the virtual private cloud \(VPC\).
    -   Change a managed Kubernetes cluster to a dedicated Kubernetes cluster, or change a professional managed Kubernetes cluster to a standard managed Kubernetes cluster.
    -   Change the network plug-in.
    -   Change the volume plug-in.
    -   Migrate applications across namespaces.
-   The following limits apply to Elastic Compute Service \(ECS\) instances in ACK clusters:
    -   ECS instances support the pay-as-you-go and subscription billing methods. Other resources, such as Server Load Balancer \(SLB\) instances, support only the pay-as-you-go billing method. You can change the billing method from pay-as-you-go to subscription in the ECS console.
    -   When you create clusters, expand clusters, or perform auto scaling of clusters, you may fail to create nodes due to resource quota limits or inventory availability.
    -   If you want to create subscription ECS instances when you create a cluster, you may fail to create the ECS instances due to resource quota limits or inventory availability. After a subscription ECS instance is created, you cannot release the ECS instance before the subscription expires.

        **Note:** To avoid these issues, we recommend that you create pay-as-you-go ECS instances when you create a cluster. You can change the billing method from pay-as-you-go to subscription in the ECS console based on your requirements.

    -   Requirements for ECS specifications: You must select ECS instances with at least 4 vCPUs and 8 GiB of memory.
-   Bandwidth limits on access to the control plane components:

    Bandwidth limits apply when you access the control pane components, including kube-apiserver and etcd, of a cluster by calling API operations or by using command lines. If you read a large number of cluster events at a time, bandwidth throttling may be triggered and you may fail to read the events. We recommend that you query cluster events in the event center. Alternatively, you can add paging parameters, such as `--chunk-size=500`, to the API request or command lines to reduce the number of events to be read. If bandwidth throttling is frequently triggered in a standard managed Kubernetes cluster, you can migrate workloads from the cluster to a professional managed Kubernetes cluster.

    For more information about the event center, see [Scenario 1: Use node-problem-detector with the Kubernetes event center of Log Service to sink cluster events](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Monitoring management/Event monitoring.md).

    For more information about how to migrate workloads from a standard managed Kubernetes cluster to a professional managed Kubernetes cluster, see [Hot migration from standard managed Kubernetes clusters to professional managed Kubernetes clusters](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Migrate to professional managed Kubernetes clusters/Hot migration from standard managed Kubernetes clusters to professional managed Kubernetes clusters.md).


## Quota limits

|Item|Quota per Alibaba Cloud account|Quota increase|
|----|-------------------------------|--------------|
|Real-name verification|Real-name verification is required.|N/A|
|Create pay-as-you-go resources|N/A|N/A|
|Total number of dedicated Kubernetes clusters and managed Kubernetes clusters|Default: 50. Maximum: 2,000.|[Navigate to the Quota Center page to submit a ticket](https://quotas.console.aliyun.com/products/csk/quotas). |
|Total number of master nodes and worker nodes in an ACK cluster|Default: 100. Maximum: 5,000.|[Navigate to the Quota Center page to submit a ticket](https://quotas.console.aliyun.com/products/csk/quotas). |
|Total number of pods on a worker node|The maximum number is configurable when you create the cluster in the console. The upper limit is 256.|N/A|
|Total number of serverless Kubernetes \(ASK\) clusters|5|[Navigate to the Quota Center page to submit a ticket](https://quotas.console.aliyun.com/products/csk/quotas). |

**Note:** The maximum number of pods on a node cannot be increased. You can use the quota increase methods above to increase the maximum number of clusters within an Alibaba Cloud account and the maximum number of nodes in a cluster.

## Quota limits on underlying cloud resources

|Category|Item|Quota for general users|Quota increase|
|--------|----|-----------------------|--------------|
|[Computing](/intl.en-US/Product Introduction/Limits.md)|Resource Orchestration Service \(ROS\) templates|Default: 100.|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
|Total vCPUs of all pay-as-you-go instances|500 vCPUs|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
|High-specification pay-as-you-go instances \(with more than 16 vCPUs\)|Unavailable for purchase. Only pay-as-you-go instances with less than 16 vCPUs can be purchased.|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
|Total vCPUs of all preemptible instances|800 vCPUs|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
|Change from pay-as-you-go to subscription|Not supported by the following instance families: t1, s1, s2, s3, c1, c2, m1, m2, n1, n2, and e3.|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
|Auto Scaling \(ESS\)|The maximum number of ECS instances in a scaling group is 2,000.|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
|Operating system| |N/A|
|[Networking](/intl.en-US/Product Introduction/Limits/Limits and quotas.md)|Number of custom route entries in a route table|48|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
|Number of vSwitches in a VPC|24|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
|Number of VPCs within an Alibaba Cloud account|10|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
|Number of private IP addresses in a VPC|65535|N/A|
|Number of IP addresses that can be added to a basic security group|2000|N/A|
|Number of elastic network interfaces \(ENIs\)|50000|N/A|
|Number of elastic IP addresses \(EIPs\) within an Alibaba Cloud account|20|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
|[SLB](/intl.en-US/Classic Load Balancer/User Guide/Limits/Limits.md)|Number of SLB instances within an Alibaba Cloud account|60|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
|Number of backend servers that can be attached to an SLB instance|200|N/A|
|Number of listeners that can be added to an SLB instance|50|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
|Number of times that an ECS instance can be repeatedly added to SLB instances as a backend server|50|N/A|
|[Elastic Block Storage \(EBS\)](/intl.en-US/Product Introduction/Limits.md)|Number of pay-as-you-go disks in all regions within an Alibaba Cloud account|The quota is five times the number of ECS instances across all regions within an Alibaba Cloud account. You can create at least 10 pay-as-you-go disks within each Alibaba Cloud account.|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
|Total capacity of all pay-as-you-go disks that are used as data disks within an Alibaba Cloud account|Subject to ECS resource usage, regions, and disk types. You can go to the Privileges & Quotas page in the ECS console to view details. For more information, see [View quotas \(old version\)]().|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |

