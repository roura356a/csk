# Billing method

Container Service for Kubernetes \(ACK\) supports four types of clusters: dedicated Kubernetes clusters, managed Kubernetes clusters, serverless Kubernetes clusters \(ASK\), and edge Kubernetes clusters. Managed Kubernetes clusters include standard managed Kubernetes clusters and professional managed Kubernetes clusters. This topic describes the billing methods of these cluster types.

## Professional managed Kubernetes clusters

If you use a professional managed Kubernetes cluster, you are charged for cluster management and cloud resources that are created in the cluster.

**Billing method**

-   The following table describes the billing methods of cluster management.

    |Billing method|Unit price|
    |--------------|----------|
    |Pay-as-you-go|USD 0.09/hour per cluster |

-   For more information about the billing methods of cloud resources, see the following topics:
    -   [ECS billing overview](/intl.en-US/Pricing/Billing overview.md)
    -   [NAT gateway billing](/intl.en-US/Pricing/Billing overview.md)
    -   [SLB pay-as-you-go billing](/intl.en-US/Classic Load Balancer/CLB Pricing/Pay-as-you-go.md)

**Overdue payments**

-   Bills

    The billing cycle of a professional managed Kubernetes cluster is one hour. You can view the billing details for the previous hour in the ACK console. The service fee is automatically deducted from your Alibaba Cloud account. A bill is issued within 10 to 30 minutes after the current billing cycle is completed.

-   Instance release due to overdue payments

    If your Alibaba Cloud account does not have a sufficient balance, the payment for the professional managed Kubernetes cluster becomes overdue. In this case, you cannot access the API server of the cluster. However, services running on worker nodes are not affected. Your professional managed Kubernetes cluster is suspended if you do not top up your account within 15 days after a payment becomes overdue. In this case, the nodes are removed from your cluster and the cluster is deleted, but the nodes are not released. The release of elastic container instances caused by cluster deletion cannot be restored.


## Dedicated Kubernetes clusters and standard Kubernetes clusters

If you use a dedicated or standard Kubernetes cluster, you are charged for only the cloud resources that are created in the cluster. The cloud resources include ECS instances, SLB instances, and NAT gateways. For more information about the billing methods of these resources, see the following topics:

-   [ECS billing overview](/intl.en-US/Pricing/Billing overview.md)
-   [NAT gateway billing](/intl.en-US/Pricing/Billing overview.md)
-   [SLB pay-as-you-go billing](/intl.en-US/Classic Load Balancer/CLB Pricing/Pay-as-you-go.md)

## ASK clusters

If you use an ASK cluster, you are charged for only the cloud resources that are created in the cluster. The cloud resources include elastic container instances, SLB instances, and NAT gateways. For more information about the billing methods of ECIs, see the following topic:

-   [ECI billing overview](https://www.alibabacloud.com/help/zh/doc-detail/89142.html)

## Edge Kubernetes clusters

Edge Kubernetes clusters are free of charge during the public review period.

