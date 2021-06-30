# Billing rules

Container Service for Kubernetes \(ACK\) provides four types of clusters: dedicated Kubernetes clusters, managed Kubernetes clusters, serverless Kubernetes \(ASK\) clusters, and edge Kubernetes clusters. Managed Kubernetes clusters include standard Kubernetes clusters and professional Kubernetes clusters. This topic describes the billing rules of these cluster types.

## Professional Kubernetes clusters

If you use a professional Kubernetes cluster, you are charged for cluster management and the cloud resources that are created for the cluster.

**Billing description**

-   The following table describes the billing of cluster management.

    |Billing method|Unit price|
    |--------------|----------|
    |Pay-as-you-go|USD 0.09/hour per cluster |

-   For more information about the billing rules of relevant cloud resources, see the following topics:
    -   [ECS billing overview](/intl.en-US/Pricing/Billing overview.md)
    -   [NAT Gateway billing](/intl.en-US/Pricing/Billing overview.md)
    -   [SLB pay-as-you-go billing](/intl.en-US/Classic Load Balancer/CLB Pricing/Pay-as-you-go.md)

**Overdue payments**

-   Bills

    The billing cycle of a professional Kubernetes cluster is 1 hour. You can view the billing details of the previous hour in the ACK console. The service fee is automatically deducted from your Alibaba Cloud account. A bill is issued within 10 to 30 minutes after the current billing cycle ends.

-   Instance releases due to overdue payments

    If your Alibaba Cloud account does not have a sufficient balance, the payment for the professional Kubernetes cluster becomes overdue. In this case, you cannot access the API server of the cluster. However, services that run on worker nodes are not affected. Your professional Kubernetes cluster is suspended if you do not top up your account within 15 days after a payment becomes overdue. In this case, the nodes are removed from your cluster and the cluster is deleted, but the nodes are not released. However, the released elastic container instances caused by cluster deletion cannot be restored.


## Dedicated Kubernetes clusters and standard Kubernetes clusters

If you use a dedicated or standard Kubernetes cluster, you are charged for only the cloud resources that are created for the cluster. The cloud resources include Elastic Compute Service \(ECS\) instances, Server Load Balancer \(SLB\) instances, and NAT gateways. For more information about the billing rules of these resources, see the following topics:

-   [ECS billing overview](/intl.en-US/Pricing/Billing overview.md)
-   [NAT Gateway billing](/intl.en-US/Pricing/Billing overview.md)
-   [SLB pay-as-you-go billing](/intl.en-US/Classic Load Balancer/CLB Pricing/Pay-as-you-go.md)

## ASK clusters

If you use an ASK cluster, you are charged for only the cloud resources that are created for the cluster. The cloud resources include elastic container instances, SLB instances, and NAT gateways. For more information about the billing rules of elastic container instances, see the following topic:

-   [Elastic Container Instance billing overview](https://www.alibabacloud.com/help/zh/doc-detail/89142.html)

## Edge Kubernetes clusters

Edge Kubernetes clusters are in public preview and are free for use.

## Registered Kubernetes clusters

If you use a registered Kubernetes cluster, you are charged for only the cloud resources that are created for the cluster. The cloud resources include ECS instances, SLB instances, elastic IP addresses \(EIPs\), and middleware resources, such Log Service projects. For more information about the billing rules of these resources, see the following topics:

-   [ECS billing overview](/intl.en-US/Pricing/Billing overview.md)
-   [SLB billing](/intl.en-US/Classic Load Balancer/CLB Pricing/Pay-as-you-go.md)
-   [Log Service billing](/intl.en-US/Pricing/Billable items and billing method.md)

