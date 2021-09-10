---
keyword: [Terraform基本概念, Provider, terraform-provider-alicloud, 应用场景]
---

# Terraform概述

Terraform是一种开源工具，用于安全高效地预览，配置和管理云基础架构和资源。本文介绍Terraform的基本概念、使用优势和应用场景。

## 基本概念

Terraform是一个云上资源编排工具，实现基础设施即代码。

-   Terraform能够让您在阿里云上轻松使用简单模板语言定义、预览和部署云基础架构。更多信息，请参见[Configuration Syntax](https://www.terraform.io/docs/configuration/syntax.html)。
-   Terraform是一个安全、高效地部署、更改、版本化基础设施和应用程序的工具，可以用来管理多层次的资源。
-   Terraform统一管理从上层的软件到底层的网络、系统的配置。
-   Terraform可以创建、修改、删除ECS、VPC、RDS、SLB等多种阿里云云产品资源。

有关Terraform应用场景的具体介绍，请参见[应用场景]()。

|Terraform资源|Terraform工具|
|-----------|-----------|
|Terraform的资源分为2类：

-   Resource：资源，指新创建的资源。
-   Data Source： 数据资源，查询已有的资源信息并获取其属性。

以下举例说明如何使用Resource和Data Source。

```
### Data Sources
# 列出2 Core 4 GB这种规格的机器的型号。
# 参考文档：https://registry.terraform.io/providers/aliyun/alicloud/latest/docs/data-sources/instance_types
data "alicloud_instance_types" "c2g4" {
  cpu_core_count = 2
  memory_size    = 4
}

## Resources
# 创建一个SLB。
# 参考文档：https://registry.terraform.io/providers/aliyun/alicloud/latest/docs/resources/instance
resource "alicloud_slb" "default" {
  name          = var.name
  specification = "slb.s2.small"
  vswitch_id    = alicloud_vswitch.default.id
}
```

关于容器服务ACK的Resources和Data Resources的相关信息，请参见[通过Terraform使用ACK](#section_d7a_kn2_sui)。

|Terraform工具分为2部分：

-   Terraform CLI：Terraform客户端命令行工具。
-   Terraform Provider：各个云厂商都提供了自己的Provider，用于将云产品接入到Terraform中。关于Terraform Provider的更多信息，请参见[Provider](https://registry.terraform.io/browse/providers)。

您可以下载Terraform工具。具体操作，请参见[在本地安装和配置Terraform]()和[在Cloud Shell中使用Terraform]()。 |

阿里云是国内第一家与Terraform集成的云厂商，阿里云Provider（[terraform-provider-alicloud](https://www.terraform.io/docs/providers/alicloud/index.html)）目前已经提供了超过163个Resource和113个Data Source，覆盖计算、存储、网络、负载均衡、CDN、容器服务、中间件、访问控制、数据库等超过35款产品，已经满足了大量大客户的自动化上云需求。

关于Terraform的更多信息，请参见[Terraform](https://www.terraform.io/)。

## 使用优势

-   将基础架构部署到多个云

    Terraform适用于多云方案，将类似的基础架构部署到阿里云、其他云提供商或者本地数据中心。开发人员能够使用相同的工具和相似的配置文件同时管理不同云提供商的资源。

-   自动化管理基础架构

    Terraform能够创建配置文件的模板，以可重复、可预测的方式定义、预配和配置ECS资源，减少因人为因素导致的部署和管理错误。通过使用Terraform，您能够多次部署同一模板，创建相同的开发、测试和生产环境。

-   基础架构即代码

    可以用代码来管理维护资源。允许保存基础设施状态，从而使您能够跟踪对系统（基础设施即代码）中不同组件所做的更改，并与其他人共享这些配置。

-   降低开发成本

    您可通过按需创建开发和部署环境来降低成本。并且，您可以在系统更改之前进行评估。


## 通过Terraform使用ACK

ACK支持通过Terraform管理以下Resource和Data Source。

|名称|描述|
|--|--|
|[alicloud\_cs\_edge\_kubernetes](https://registry.terraform.io/providers/aliyun/alicloud/latest/docs/resources/cs_edge_kubernetes)|管理ACK边缘托管版集群。|
|[alicloud\_cs\_kubernetes\_node\_pool](https://registry.terraform.io/providers/aliyun/alicloud/latest/docs/resources/cs_kubernetes_node_pool)|管理ACK节点池。|
|[alicloud\_cs\_kubernetes\_permissions](https://registry.terraform.io/providers/aliyun/alicloud/latest/docs/resources/cs_kubernetes_permissions)|管理ACK集群内RBAC权限。|
|[alicloud\_cs\_managed\_kubernetes](https://registry.terraform.io/providers/aliyun/alicloud/latest/docs/resources/cs_managed_kubernetes)|管理ACK托管版集群。|
|[alicloud\_cs\_kubernetes](https://registry.terraform.io/providers/aliyun/alicloud/latest/docs/resources/cs_kubernetes)|管理ACK专有版集群。|
|[alicloud\_cs\_serverless\_kubernetes](https://registry.terraform.io/providers/aliyun/alicloud/latest/docs/resources/cs_serverless_kubernetes)|管理ASK集群。|

|名称|描述|
|--|--|
|[alicloud\_ack\_service](https://registry.terraform.io/providers/aliyun/alicloud/latest/docs/data-sources/ack_service)|开通容器服务ACK。|
|[alicloud\_cs\_edge\_kubernetes\_clusters](https://registry.terraform.io/providers/aliyun/alicloud/latest/docs/data-sources/cs_edge_kubernetes_clusters)|列举所有的ACK边缘托管版集群。|
|[alicloud\_cs\_kubernetes\_clusters](https://registry.terraform.io/providers/aliyun/alicloud/latest/docs/data-sources/cs_kubernetes_clusters)|列举所有的ACK专有版集群。|
|[alicloud\_cs\_kubernetes\_permissions](https://registry.terraform.io/providers/aliyun/alicloud/latest/docs/data-sources/cs_kubernetes_permissions)|列举RAM用户拥有的所有权限。|
|[alicloud\_cs\_managed\_kubernetes\_clusters](https://registry.terraform.io/providers/aliyun/alicloud/latest/docs/data-sources/cs_managed_kubernetes_clusters)|列举所有的ACK托管版集群。|
|[alicloud\_cs\_serverless\_kubernetes\_clusters](https://registry.terraform.io/providers/aliyun/alicloud/latest/docs/data-sources/cs_serverless_kubernetes_clusters)|列举所有的ASK集群。|

**相关文档**  


[在Cloud Shell中使用Terraform]()

[在本地安装和配置Terraform]()

[使用Terraform创建Kubernetes托管版集群](/intl.zh-CN/Terraform/使用Terraform创建Kubernetes托管版集群.md)

[使用Terraform创建零Worker节点集群](/intl.zh-CN/Terraform/使用Terraform创建零Worker节点集群.md)

[使用Terraform创建具备自动伸缩功能的节点池](/intl.zh-CN/Terraform/使用Terraform创建具备自动伸缩功能的节点池.md)

