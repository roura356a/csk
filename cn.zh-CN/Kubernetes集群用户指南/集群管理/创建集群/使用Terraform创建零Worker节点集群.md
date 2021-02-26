---
keyword: [0 Worker节点, terraform, k8s集群, 节点池]
---

# 使用Terraform创建零Worker节点集群

如果您想创建ACK集群又想节省ECS资源，您可以创建零Worker节点的ACK集群。ACk Pro版托管集群支持创建零Worker节点集群。您也可以通过使用Terraform创建零Worker节点的ACK集群。本文介绍如何通过Terraform工具创建零Worker节点的ACK集群，并在后期通过创建节点池为集群添加节点。

-   您已安装Terraform工具。具体操作，请参见[t41667.dita\#task\_bts\_tlz\_dfb]()。
-   如果您需要使用自动伸缩节点池来管理节点，由于自动伸缩功能依赖弹性伸缩（Auto Scaling，旧称ESS）服务，启动节点自动伸缩前，您需要开通弹性伸缩服务，并完成默认角色授权。具体操作，请参见[开通弹性伸缩服务](/cn.zh-CN/Kubernetes集群用户指南/弹性伸缩/节点自动伸缩.md)。

ACk Pro版托管集群支持创建零Worker节点。零Worker节点集群有以下优点：

-   成本更低 ：在最大程度上保证您的利益，同时也节约了成本。
-   配置更为灵活 ：零Worker节点集群，托管配置和用户节点配置分离，用户能更加灵活的配置管理集群和集群节点。

Terraform是一种开源工具，通过Provider来支持新的基础架构，用于安全高效地预览，配置和管理云基础架构和资源。更多信息，请参见[什么是Terraform](https://help.aliyun.com/document_detail/95820.html)。Alibaba Terraform Provider从1.109.1版本开始提供了创建零Worker节点集群的能力，下面详细展示如何通过Terraform来创建零Worker节点的集群，然后在通过节点池来管理Worker节点。

## 使用Terraform创建零Worker节点集群

使用Terraform创建零Worker节点集群的配置文件如下。

```
variable "name" {
  default = "zero-worker-cluster"
}

data "alicloud_zones" default {
  available_resource_creation  = "VSwitch"
}

resource "alicloud_vpc" "default" {
  name                         = var.name
  cidr_block                   = "10.x.x.x/xx"
}

resource "alicloud_vswitch" "default" {
  name                         = var.name
  vpc_id                       = alicloud_vpc.default.id
  cidr_block                   = "10.x.x.x/xx"
  availability_zone            = "data.alicloud_zones.default.zones.0.id
}

resource "alicloud_key_pair" "default" {
  key_name                     = var.name
}

resource "alicloud_cs_managed_kubernetes" "default" {
  name                         = var.name
  count                        = 1
  # 只有ACK Pro托管版集群才支持创建0节点集群。
  cluster_spec                 = "ack.pro.small"
  password                     = "Hello1234"
  pod_cidr                     = "172.20.0.0/16"
  service_cidr                 = "172.21.0.0/20"
  is_enterprise_security_group = true
  # 创建零Worker节点的集群，需要传节点使用的Vswitch。
  worker_vswitch_ids           = [alicloud_vswitch.default.id]
}
```

## 通过节点池为集群添加节点

当您创建完零Worker节点的ACK集群后，想添加节点时，您可以通过Terraform创建节点池来为集群添加Worker节点。配置方式有以下两种：

-   通过自定义节点池来管理Worker节点的配置如下。

    ```
    # 依赖步骤一中的变量以及配置。
    resource "alicloud_cs_kubernetes_node_pool" "custom-np" {
      name                         = var.name
      cluster_id                   = alicloud_cs_managed_kubernetes.default.0.id
      vswitch_ids                  = [alicloud_vswitch.default.id]
      instance_types               = [data.alicloud_instance_types.default.instance_types.0.id]
      system_disk_category         = "cloud_efficiency"
      system_disk_size             = 40
      key_name                     = alicloud_key_pair.default.key_name
    
      # 为节点池创建1个Worker节点。
      node_count                   = 1
    }
    ```

-   通过自动伸缩节点池来管理Worker节点的配置如下。

    **说明：** 使用自动伸缩节点池，必须要先开启本文[前提条件](#prereq_yjz_13n_fb1)中提到的ESS权限。

    ```
    resource "alicloud_cs_kubernetes_node_pool" "auto-np" {
      name                         = var.name
      cluster_id                   = alicloud_cs_managed_kubernetes.default.0.id
      vswitch_ids                  = [alicloud_vswitch.default.id]
      instance_types               = [data.alicloud_instance_types.default.instance_types.0.id]
      system_disk_category         = "cloud_efficiency"
      system_disk_size             = 40
      key_name                     = alicloud_key_pair.default.key_name
      
      # automatic scaling node pool configuration.
      scaling_config {
        min_size      = 1
        max_size      = 10
      }
    
    }
    ```


**相关文档**  


[使用Terraform创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/使用Terraform创建Kubernetes托管版集群.md)

[阿里云容器服务Terraform资源](https://registry.terraform.io/providers/aliyun/alicloud/latest/docs/resources/cs_kubernetes)

[阿里云Terraform Provider](https://github.com/hashicorp/terraform-provider-alicloud)

[使用Terraform创建自动伸缩节点池](/cn.zh-CN/Kubernetes集群用户指南/节点管理/节点池管理/使用Terraform创建自动伸缩节点池.md)

