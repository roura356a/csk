# Limits {#concept_gsf_w2b_5db .concept}

Limits for Alibaba Cloud Container Service Kubernetes clusters are as follows.

## Clusters {#section_ldw_hfb_5db .section}

-   By default, you can create up to five clusters in all regions and add up to 40 nodes to each cluster. To create more clusters or add more nodes to a cluster, please open a ticket.
-   Currently, Kubernetes clusters only support Linux containers. The support for Windows containers is planned.
-   Currently, Kubernetes clusters only support Virtual Private Cloud \(VPC\). You can create a VPC or use an existing VPC when creating a Kubernetes cluster.
-   Currently, Elastic Compute Service \(ECS\) instances support the following two billing methods: Pay-As-You-Go and Subscription. You can change the billing method of your instance from Pay-As-You-Go to Subscription in the ECS console. Other resources such as Server Load Balancer \(SLB\) instances support only the Pay-As-You-Go billing method.

## ECS instances {#section_fth_hfb_5db .section}

-   Only support the CentOS operating system.
-   Limits for adding an existing node:
    -   The ECS instance to be added must be in the same region as the cluster and use VPC.
    -   When adding an existing ECS instance, make sure that your ECS instance has an Elastic IP \(EIP\) for the network type VPC. Otherwise, the ECS instance fails to be added.
    -   The ECS instance to be added must be under the same account as the cluster.

## Cluster expansion and contraction {#section_cnt_pfb_5db .section}

-   The number of worker nodes must be in the range of 1 to 37.
-   Manual and automatic cluster elastic scaling are supported.
-   Currently, master nodes in a Kubernetes cluster cannot be expanded automatically.
-   According to the Resource Orchestration Service \(ROS\) rules, elastic contraction does not contract the nodes created when the cluster is created or the nodes manually added to the cluster, but only contracts the nodes manually expanded by you. The contraction rule is based on the creation time. When you contract a cluster, the worker nodes are removed from the cluster in the order that they are added when you expand the cluster.
-   Nodes added through cluster expansion are Pay-As-You-Go nodes.

