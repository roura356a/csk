# Billing

Alibaba Cloud Container Service for Kubernetes \(ACK\) supports four types of clusters: dedicated clusters, managed clusters, serverless clusters, and edge clusters. Managed clusters include standard managed clusters and professional managed clusters. This topic describes the billing methods of the clusters of these types.

## Professional managed clusters

If you use a professional managed cluster, you are charged for cluster management and cloud resources that are created in the cluster.

**Billing methods**

-   The following table describes the billing methods of cluster management.

    |Billing method|Price|
    |--------------|-----|
    |Pay-as-you-go|USD 0.09/hour per cluster |

-   For information about the billing methods of cloud resources, see the following topics:
    -   [Elastic Compute Service \(ECS\) billing overview](/intl.en-US/Pricing/Billing items/Billing overview.md)
    -   [Network Address Translation \(NAT\) gateway billing](/intl.en-US/Pricing/Billing.md)
    -   [Server Load Balancer \(SLB\) pay-as-you-go billing](/intl.en-US/Pricing/Billing method.md)

**Overdue payments**

-   Bills

    The billing cycle of a professional managed cluster is one hour. You can view the billing details for the previous hour in the ACK console. The service fee is automatically deducted from the balance of your Alibaba Cloud account. A bill is issued within 10 to 30 minutes after the current billing cycle is completed.

-   Instance release due to overdue payments

    If your Alibaba Cloud account does not have sufficient balance for the bill, you have an overdue payment for the professional managed cluster. In this case, you cannot access the API server of the cluster. However, services running on worker nodes are not affected. Your professional managed cluster is suspended if you do not top up your account within 15 days after a payment becomes overdue. In this case, the nodes are removed from your cluster and the cluster is deleted, but the nodes are not released. The release of elastic container instances \(ECIs\) caused by cluster deletion cannot be restored.


## Dedicated clusters and standard clusters

If you use a dedicated or standard cluster, you are charged for only the cloud resources that are created in the cluster. The cloud resources include ECS instances, SLB instances, and NAT gateways. For more information about the billing methods of these resources, see the following topics:

-   [ECS billing overview](/intl.en-US/Pricing/Billing items/Billing overview.md)
-   [NAT gateway billing](/intl.en-US/Pricing/Billing.md)
-   [SLB pay-as-you-go billing](/intl.en-US/Pricing/Billing method.md)

## Serverless clusters

If you use a serverless cluster, you are charged for only the cloud resources that are created in the cluster. The cloud resources include ECIs, SLB instances, and NAT gateways. For more information about the billing methods of ECIs, see the following topic:

-   [ECI billing overview](https://www.alibabacloud.com/help/zh/doc-detail/89142.html)

## Edge clusters

Edge clusters are currently in public preview. During this period, you can use them for free.

