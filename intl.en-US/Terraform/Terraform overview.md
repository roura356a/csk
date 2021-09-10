---
keyword: [Terraform concepts, provider, terraform-provider-alicloud, scenarios]
---

# Terraform overview

Terraform is an open source tool used to securely and efficiently preview, configure, and manage cloud infrastructure and resources. This topic introduces the basic concepts, advantages, and scenarios of Terraform.

## Basic concepts

Terraform is an infrastructure as code \(IaC\) software tool for cloud resource orchestration.

-   Terraform allows you to use a simple template language to define, preview, and deploy cloud infrastructure on Alibaba Cloud. For more information, see [Configuration Syntax](https://www.terraform.io/docs/configuration/syntax.html).
-   Terraform is a tool that is used to deploy, change, and perform versioning on infrastructure and applications in a safe and efficient manner. The tool can be used for multi-level resource management.
-   The resources that Terraform manages include upper-layer software and the configurations of underlying network and system configurations.
-   Terraform can be used to create, change, and delete various Alibaba Cloud resources, such as resources of Elastic Compute Service \(ECS\), Virtual Private Cloud \(VPC\), Relational Database Service \(RDS\), and Server Load Balancer \(SLB\).

For more information about the scenarios of Terraform, see [Scenarios]().

|Terraform resource|Terraform plug-in|
|------------------|-----------------|
|Resources have two types in Terraform.

-   Resources are newly created resource objects.
-   Data sources allow Terraform to query existing resource information and retrieve resource attributes.

The following example shows how to use resources and data sources.

```
### Data Sources
# List the instance types that have 2 cores and 4 GB of memory. 
# Reference: https://registry.terraform.io/providers/aliyun/alicloud/latest/docs/data-sources/instance_types
data "alicloud_instance_types" "c2g4" {
  cpu_core_count = 2
  memory_size    = 4
}

## Resources
# Create an SLB instance. 
# Reference: https://registry.terraform.io/providers/aliyun/alicloud/latest/docs/resources/instance
resource "alicloud_slb" "default" {
  name          = var.name
  specification = "slb.s2.small"
  vswitch_id    = alicloud_vswitch.default.id
}
```

For more information about the resources and data sources of Container Service for Kubernetes \(ACK\), see [Use ACK with Terraform](#section_d7a_kn2_sui).

|Terraform provides two types of plug-in.

-   Terraform CLI is the command line tool used in Terraform.
-   Terraform providers are used to interface cloud resources with Terraform. Providers are developed and provided by cloud service providers. For more information about Terraform providers, see [Provider](https://registry.terraform.io/browse/providers).

For more information about how to download Terraform plug-ins, see [Install and configure Terraform in the local PC]() and [Use Terraform in Cloud Shell](). |

Alibaba Cloud is the first cloud service provider in China to integrate Terraform with its services. The Alibaba Cloud Provider \([terraform-provider-alicloud](https://www.terraform.io/docs/providers/alicloud/index.html)\) provides 163 resources and 113 data sources, and covers 35 Alibaba Cloud services, such as computing, storage, networking, load balancing, CDN, middleware, access control, and database services. This meets the requirements of many big customers in cloud deployment automation.

For more information about how to use Terraform, see [Terraform](https://www.terraform.io/).

## Advantages

-   Multi-cloud infrastructure deployment

    Terraform is a popular tool of choice for multi-cloud scenarios in which similar infrastructures are deployed to Alibaba Cloud, third-party clouds, or data centers. Terraform allows developers to use the same tools and similar configuration files to manage infrastructure resources that are built on clouds of different providers.

-   Automated infrastructure management

    Terraform can be used to create configuration file templates to repeatedly define, provision, and configure ECS resources in a predictable manner. This reduces human errors during deployment and management operations. Terraform allows you to deploy a template multiple times to create the same development, testing, and production environments.

-   Infrastructure as code

    In Terraform, you can use code to manage and maintain resources. Terraform stores a copy of the current state of your infrastructure. This way, you can track changes made to components in the system \(infrastructure as code\) and share infrastructure configurations with other users.

-   Reduced development costs

    You can use Terraform to create development and deployment environments based on your requirements to reduce costs. You can also evaluate development costs before you make changes to the system.


## Use ACK with Terraform

ACK allows you to manage the following resources and data sources by using Terraform.

|Name|Description|
|----|-----------|
|[alicloud\_cs\_edge\_kubernetes](https://registry.terraform.io/providers/aliyun/alicloud/latest/docs/resources/cs_edge_kubernetes)|Manages managed edge Kubernetes clusters.|
|[alicloud\_cs\_kubernetes\_node\_pool](https://registry.terraform.io/providers/aliyun/alicloud/latest/docs/resources/cs_kubernetes_node_pool)|Manages node pools.|
|[alicloud\_cs\_kubernetes\_permissions](https://registry.terraform.io/providers/aliyun/alicloud/latest/docs/resources/cs_kubernetes_permissions)|Manages Role-Based Access Control \(RBAC\) permissions.|
|[alicloud\_cs\_managed\_kubernetes](https://registry.terraform.io/providers/aliyun/alicloud/latest/docs/resources/cs_managed_kubernetes)|Manages managed Kubernetes clusters.|
|[alicloud\_cs\_kubernetes](https://registry.terraform.io/providers/aliyun/alicloud/latest/docs/resources/cs_kubernetes)|Manages dedicated Kubernetes clusters.|
|[alicloud\_cs\_serverless\_kubernetes](https://registry.terraform.io/providers/aliyun/alicloud/latest/docs/resources/cs_serverless_kubernetes)|Manages serverless Kubernetes clusters.|

|Name|Description|
|----|-----------|
|[alicloud\_ack\_service](https://registry.terraform.io/providers/aliyun/alicloud/latest/docs/data-sources/ack_service)|Activates ACK.|
|[alicloud\_cs\_edge\_kubernetes\_clusters](https://registry.terraform.io/providers/aliyun/alicloud/latest/docs/data-sources/cs_edge_kubernetes_clusters)|Lists all managed edge Kubernetes clusters.|
|[alicloud\_cs\_kubernetes\_clusters](https://registry.terraform.io/providers/aliyun/alicloud/latest/docs/data-sources/cs_kubernetes_clusters)|Lists all dedicated Kubernetes clusters.|
|[alicloud\_cs\_kubernetes\_permissions](https://registry.terraform.io/providers/aliyun/alicloud/latest/docs/data-sources/cs_kubernetes_permissions)|Lists all permissions of specified \(Resource Access Management\) RAM users.|
|[alicloud\_cs\_managed\_kubernetes\_clusters](https://registry.terraform.io/providers/aliyun/alicloud/latest/docs/data-sources/cs_managed_kubernetes_clusters)|Lists all managed Kubernetes clusters.|
|[alicloud\_cs\_serverless\_kubernetes\_clusters](https://registry.terraform.io/providers/aliyun/alicloud/latest/docs/data-sources/cs_serverless_kubernetes_clusters)|Lists all serverless Kubernetes clusters.|

**Related topics**  


[Use Terraform in Cloud Shell]()

[Install and configure Terraform in the local PC]()

[Use Terraform to create a managed Kubernetes cluster](/intl.en-US/Terraform/Use Terraform to create a managed Kubernetes cluster.md)

[Use Terraform to create an ACK cluster without a worker node](/intl.en-US/Terraform/Use Terraform to create an ACK cluster without a worker node.md)

[Use Terraform to create an auto-scaling node pool](/intl.en-US/Terraform/Use Terraform to create an auto-scaling node pool.md)

