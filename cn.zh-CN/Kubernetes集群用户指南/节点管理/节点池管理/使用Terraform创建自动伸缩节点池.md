---
keyword: [Terraform, 自动伸缩节点池, Alibaba Cloud Provider, 创建]
---

# 使用Terraform创建自动伸缩节点池

自动伸缩节点池中的节点具备自动伸缩能力。您可以通过使用Terraform工具创建自动伸缩节点池。本文介绍如何通过Terraform创建自动伸缩节点池。

-   您已安装Terraform工具。具体操作，请参见[t41667.dita\#task\_bts\_tlz\_dfb]()。
-   自动伸缩功能依赖弹性伸缩（Auto Scaling，旧称ESS）服务。启动节点自动伸缩前，您需要开通弹性伸缩服务，并完成默认角色授权。具体操作，请参见[开通弹性伸缩服务](/cn.zh-CN/Kubernetes集群用户指南/弹性伸缩/节点自动伸缩.mdstep_t9t_hu3_cbw)。

    **说明：** 如果您之前已经使用了alicloud\_cs\_kubernetes\_autoscaler组件，默认已开通弹性伸缩服务。

-   在RAM控制台，为当前集群添加弹性伸缩服务授权策略。关于如何授权的详细信息，请参见[授权](/cn.zh-CN/Kubernetes集群用户指南/弹性伸缩/节点自动伸缩.mdstep_dkz_59w_xec)。

Terraform是一种开源工具，通过Provider来支持新的基础架构，用于安全高效地预览，配置和管理云基础架构和资源。更多信息，请参见[什么是Terraform]()。

在[Alibaba Cloud Provider](https://registry.terraform.io/providers/aliyun/alicloud/latest)的老版本中，ACK提供了一个名为[alicloud\_cs\_kubernetes\_autoscaler](https://registry.terraform.io/providers/aliyun/alicloud/latest/docs/resources/cs_kubernetes_autoscaler)的组件。alicloud\_cs\_kubernetes\_autoscaler组件可以实现节点的弹性伸缩，但是其能力受限：

-   配置复杂，使用成本高。
-   伸缩的节点都会被放置到默认节点池，自动伸缩的节点未单独维护。
-   部分配置参数不可更改。

Alibaba Terraform Provider从1.111.0版本开始可通过组件[alicloud\_cs\_kubernetes\_node\_pool](https://registry.terraform.io/providers/aliyun/alicloud/latest/docs/resources/cs_kubernetes_node_pool)创建自动伸缩类型的节点池，优势如下：

-   配置简单，您只需要配置伸缩组内节点数的上下限。
-   针对非必须配置，ACK使用默认值的配置，以防误操作带来的基础环境不一致的问题，例如：操作系统镜像。
-   在ACK控制台中可以直观地观察自动伸缩节点池内节点的变化。

## 使用Terraform创建自动伸缩节点池

1.  如果您的集群之前已经使用alicloud\_cs\_kubernetes\_autoscaler组件，在完成上述为当前集群添加弹性伸缩服务授权后，您需要执行以下步骤平滑切换alicloud\_cs\_kubernetes\_autoscaler至alicloud\_cs\_kubernetes\_node\_pool，以创建自动伸缩类型的节点池。

**说明：** 如果您未在集群中使用alicloud\_cs\_kubernetes\_autoscaler组件，则跳过以下步骤1和步骤2，请直接进行[步骤3](#step_r9b_l96_a5y)的操作。

2.  修改集群的autoscaler-meta配置项。

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

    2.  在控制台左侧导航栏中，单击**集群**。

    3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

    4.  在集群管理页左侧导航栏中，选择**配置管理** \> **配置项**。

    5.  在配置项页面左上角的**命名空间**下拉框中，选择**kube-system**，然后在autoscaler-meta配置项右侧**操作**列下，单击**编辑**。

    6.  在编辑面板中，修改**autoscaler-meta**配置项的值。

        您需将`taints`值的String类型改成数组类型，即在**值**文本框中，修改`"taints":""`为`"taints":[]`。

    7.  单击**确定**。

3.  同步节点池。

    1.  在集群管理页左侧导航栏中，选择**节点管理** \> **节点池**。

    2.  在节点池页面右上方，单击**同步节点池**。

4.  如果您未在集群中使用alicloud\_cs\_kubernetes\_autoscaler组件，请直接进行以下操作。
5.  使用Terraform创建自动伸缩节点池。

    1.  创建自动伸缩节点池的配置文件。

        -   在已有集群中创建自动伸缩节点池，配置示例如下。

            ```
            provider "alicloud" {
              access_key = ""
              secret_key = ""
              region     = "cn-hangzhou"
            }
            # 为已有集群创建自动伸缩类型的节点池。
            resource "alicloud_cs_kubernetes_node_pool" "at1" {
              # 目标集群ID
              cluster_id           = ""
              name                 = "np-test"
              # 节点池内节点使用的vswitch，至少提供一个。
              vswitch_ids          = ["vsw-bp1mdigyhmilu2h4v****"]
              instance_types       = ["ecs.e3.medium"]
              password             = "Hello1234"
             
              scaling_config {
                # 最小节点数
                min_size     = 1
                # 最大节点数
                max_size     = 5
              }
            
            }
            ```

        -   如果未创建集群，创建自动伸缩节点池的配置示例如下。

            ```
            provider "alicloud" {
              access_key = ""
              secret_key = ""
              region     = ""
            }
            
            variable "name" {
              default    = "tf-test"
            }
            
            data "alicloud_zones" default {
              available_resource_creation  = "VSwitch"
            }
            data "alicloud_instance_types" "default" {
              availability_zone            = data.alicloud_zones.default.zones.0.id
              cpu_core_count               = 2
              memory_size                  = 4
              kubernetes_node_role         = "Worker"
            }
            
            resource "alicloud_vpc" "default" {
              name                         = var.name
              cidr_block                   = "10.1.0.0/21"
            }
            resource "alicloud_vswitch" "default" {
              name                         = var.name
              vpc_id                       = alicloud_vpc.default.id
              cidr_block                   = "10.1.1.0/24"
              availability_zone            = "data.alicloud_zones.default.zones.0.id
            }
            resource "alicloud_key_pair" "default" {
              key_name                     = var.name
            }
            
            # 创建托管版集群。
            resource "alicloud_cs_managed_kubernetes" "default" {
              name                         = var.name
              count                        = 1
              cluster_spec                 = "ack.pro.small"
              is_enterprise_security_group = true
              worker_number                = 2
              password                     = "Hello1234"
              pod_cidr                     = "172.20.0.0/16"
              service_cidr                 = "172.21.0.0/20"
              worker_vswitch_ids           = [alicloud_vswitch.default.id]
              worker_instance_types        = [data.alicloud_instance_types.default.instance_types.0.id]
            }
            
            # 为集群创建一个开启自动伸缩的节点池。
            resource "alicloud_cs_kubernetes_node_pool" "at1" {
              cluster_id                   = alicloud_cs_managed_kubernetes.default.0.id
              name                         = var.name
              vswitch_ids                  = [alicloud_vswitch.default.id]
              instance_types               = [data.alicloud_instance_types.default.instance_types.0.id]
              key_name                     = alicloud_key_pair.default.key_name
             
              scaling_config {
                min_size     = 1
                max_size     = 5
              }
            
            }
            ```

    2.  执行`terraform apply`命令完成创建。

    完成创建自动伸缩节点池后，在节点池列表中可以看到新建的自动伸缩类型节点池，该节点池名称下标注**已开启自动伸缩**。

    ![nodepool](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3272339061/p207806.png)


**相关文档**  


[节点池概述](/cn.zh-CN/Kubernetes集群用户指南/节点管理/节点池管理/节点池概述.md)

[节点自动伸缩](/cn.zh-CN/Kubernetes集群用户指南/弹性伸缩/节点自动伸缩.md)

[t2005064.md\#]()

[alicloud\_cs\_kubernetes\_node\_pool](https://registry.terraform.io/providers/aliyun/alicloud/latest/docs/resources/cs_kubernetes_node_pool)

[alicloud\_cs\_kubernetes\_autoscaler](https://registry.terraform.io/providers/aliyun/alicloud/latest/docs/resources/cs_kubernetes_autoscaler)

