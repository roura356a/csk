---
keyword: [zero worker node, Terraform, K8s cluster, node pool]
---

# Use Terraform to create an ACK cluster without a worker node

You can create a Container Service for Kubernetes \(ACK\) cluster without a worker node to reduce the cost of Elastic Compute Service \(ECS\) instances. When you create a professional managed Kubernetes cluster, you can set the number of worker nodes to zero. You can also create an ACK cluster without a worker node by using Terraform. This topic describes how to create an ACK cluster without a worker node by using Terraform. After the cluster is created, you can also add nodes by creating node pools.

-   Terraform is installed. For more information, see [t41667.dita\#task\_bts\_tlz\_dfb]().
-   If you want to manage nodes by using auto-scaling node pools, make sure that Auto Scaling \(ESS\) is activated and the ESS default role is assigned to your account. The auto scaling of nodes and node pools is dependent on ESS. For more information, see [Activate ESS](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/Auto scaling of nodes.md).

When you create a professional managed Kubernetes cluster, you can set the number of worker nodes to zero. A cluster without a worker node provides the following benefits:

-   Cost-effectiveness. A cluster without a worker node is cost-effective and meets your requirements.
-   Flexible configurations. A cluster without a worker node decouples the management configurations from node configurations. This allows you to manage the cluster and nodes in a more flexible manner.

Terraform is an open source tool that supports new infrastructures through Terraform providers. You can use Terraform to preview, configure, and manage cloud infrastructures and resources. For more information, see [What is Terraform?](https://help.aliyun.com/document_detail/95820.html). Alibaba Cloud Provider 1.109.1 and later of Terraform allows you to create an ACK cluster without a worker node. The following example describes how to create an ACK cluster without a worker node by using Terraform. After the cluster is created, you can add and manage worker nodes by using node pools.

## Create an ACK cluster without a worker node by using Terraform

The following sample code provides an example on how to create an ACK cluster without a worker node by using Terraform:

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
  # Only professional managed Kubernetes clusters allow you to specify zero worker node in the cluster configurations.
  cluster_spec                 = "ack.pro.small"
  password                     = "Hello1234"
  pod_cidr                     = "172.20.0.0/16"
  service_cidr                 = "172.21.0.0/20"
  is_enterprise_security_group = true
  # The IDs of vSwitches for worker nodes in the cluster.
  worker_vswitch_ids           = [alicloud_vswitch.default.id]
}
```

## Add nodes to an ACK cluster without a worker node by using node pools

After the ACK cluster without a worker node is created, you can create a node pool by using Terraform to add nodes to the cluster. Create a node pool by using the following methods:

-   Create a custom node pool. The following template is an example.

    ```
    # The node pool configurations are based on the preceding variables and configurations of the cluster.
    resource "alicloud_cs_kubernetes_node_pool" "custom-np" {
      name                         = var.name
      cluster_id                   = alicloud_cs_managed_kubernetes.default.0.id
      vswitch_ids                  = [alicloud_vswitch.default.id]
      instance_types               = [data.alicloud_instance_types.default.instance_types.0.id]
      system_disk_category         = "cloud_efficiency"
      system_disk_size             = 40
      key_name                     = alicloud_key_pair.default.key_name
    
      # Specify one worker node in the node pool.
      node_count                   = 1
    }
    ```

-   Create an auto-scaling node pool. The following template is an example.

    **Note:** To create an auto-scaling node pool, you must first activate ESS and grant the required permissions as described in the [Prerequisites](#prereq_yjz_13n_fb1) section.

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


**Related topics**  


[t2005064.md\#]()

[Terraform resources for ACK](https://registry.terraform.io/providers/aliyun/alicloud/latest/docs/resources/cs_kubernetes)

[Alibaba Cloud Provider of Terraform](https://github.com/hashicorp/terraform-provider-alicloud)

[t2021375.md\#]()

