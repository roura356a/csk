---
keyword: [Terraform, Kubernetes托管版集群]
---

# 使用Terraform创建Kubernetes托管版集群

Terraform是HashiCorp公司提供的一种开源工具，用于安全高效地预览，配置和管理云基础架构和资源，帮助开发者自动化地创建、更新阿里云基础设施资源，并进行版本管理。本文介绍如何使用Terraform创建ACK的托管版Kubernetes集群。

-   安装Terraform。

    **说明：** 请确认Terraform版本不低于v0.12.28，可通过`terraform --version`命令查看Terraform版本。

    -   Cloud Shell默认安装配置了Terraform和阿里云账号信息，无需任何额外配置。
    -   如果您不使用Cloud Shell，关于安装Terraform的方式，请参见[在本地安装和配置Terraform]()。
-   配置阿里云账号信息。创建环境变量，用于存放身份认证信息。

    ```
    export ALICLOUD_ACCESS_KEY="************"
    export ALICLOUD_SECRET_KEY="************"
    export ALICLOUD_REGION="cn-beijing"
    ```

    **说明：** 为提高权限管理的灵活性和安全性，建议您创建名为Terraform的RAM用户，并为该RAM用户创建AccessKey和授权。具体步骤，请参见[创建RAM用户](/intl.zh-CN/用户管理/创建RAM用户.md)和[为RAM用户授权](/intl.zh-CN/用户管理/为RAM用户授权.md)。


## 使用Terraform创建ACK托管版集群

1.  创建一个工作目录，并且在工作目录中创建以下名为main.tf的配置文件。

    main.tf文件描述了以下的Terraform配置：

    -   创建一个新的VPC，以及相关VSwitch。
    -   创建一个托管版ACK集群，包含2个Worker节点。
    ```
    provider "alicloud" {
    }
    variable "k8s_name_prefix" {
      description = "The name prefix used to create managed kubernetes cluster."
      default     = "tf-ack"
    }
    resource "random_uuid" "this" {}
    # 默认资源名称。
    locals {
      k8s_name     = substr(join("-", [var.k8s_name_prefix, random_uuid.this.result]), 0, 63)
      new_vpc_name = "vpc-for-${local.k8s_name}"
      new_vsw_name = "vsw-for-${local.k8s_name}"
      log_project_name = "log-for-${local.k8s_name}"
    }
    # 节点ECS实例配置。
    data "alicloud_instance_types" "default" {
      cpu_core_count       = 4
      memory_size          = 8
      kubernetes_node_role = "Worker"
    }
    // 满足实例规格的AZ。
    data "alicloud_zones" "default" {
      available_instance_type = data.alicloud_instance_types.default.instance_types[0].id
    }
    # 专有网络。
    resource "alicloud_vpc" "default" {
      name       = local.new_vpc_name
      cidr_block = "10.1.0.0/21"
    }
    # 交换机。
    resource "alicloud_vswitch" "vswitches" {
      name              = local.new_vsw_name
      vpc_id            = alicloud_vpc.default.id
      cidr_block        = "10.1.1.0/24"
      availability_zone = data.alicloud_zones.default.zones[0].id
    }
    # 日志服务。
    resource "alicloud_log_project" "log" {
      name        = local.log_project_name
      description = "created by terraform for managedkubernetes cluster"
    }
    # Kubernetes托管版。
    resource "alicloud_cs_managed_kubernetes" "default" {
      # Kubernetes集群名称。
      name                      = local.k8s_name
      # 新的Kubernetes集群将位于的vswitch。指定一个或多个vswitch的ID。它必须在availability_zone指定的区域中。
      worker_vswitch_ids        = split(",", join(",", alicloud_vswitch.vswitches.*.id))
      # 是否在创建kubernetes集群时创建新的nat网关。默认为true。
      new_nat_gateway           = true
      # 节点的ECS实例类型。
      worker_instance_types     = [data.alicloud_instance_types.default.instance_types[0].id]
      # Kubernetes群集的总工作节点数。默认值为3。最大限制为50。
      worker_number             = 2
      # ssh登录群集节点的密码。
      password                  = "Yourpassword1234"
      # pod网络的CIDR块。当cluster_network_type设置为flannel，你必须设定该参数。它不能与VPC CIDR相同，并且不能与VPC中的Kubernetes群集使用的CIDR相同，也不能在创建后进行修改。群集中允许的最大主机数量：256。
      pod_cidr                  = "172.20.0.0/16"
      # 服务网络的CIDR块。它不能与VPC CIDR相同，不能与VPC中的Kubernetes群集使用的CIDR相同，也不能在创建后进行修改。
      service_cidr              = "172.21.0.0/20"
      # 是否为kubernetes的节点安装云监控。
      install_cloud_monitor     = true
      # 是否为API Server创建Internet负载均衡。默认为false。
      slb_internet_enabled      = true
      # 节点的系统磁盘类别。其有效值为cloud_ssd和cloud_efficiency。默认为cloud_efficiency。
      worker_disk_category      = "cloud_efficiency"
      # 节点的数据磁盘类别。其有效值为cloud_ssd和cloud_efficiency，如果未设置，将不会创建数据磁盘。
      worker_data_disk_category = "cloud_ssd"
      # 节点的数据磁盘大小。有效值范围[20〜32768]，以GB为单位。当worker_data_disk_category被呈现，则默认为40。
      worker_data_disk_size     = 200
      # 日志配置。
      addons {
        name     = "logtail-ds"
        config   = "{\"IngressDashboardEnabled\":\"true\",\"sls_project_name\":alicloud_log_project.log.name}"
      }
    }
    ```

2.  执行以下命令初始化Terraform运行环境。

    ```
    terraform init
    ```

    输出：

    ```
    Initializing the backend...
    
    Initializing provider plugins...
    - Checking for available provider plugins...
    - Downloading plugin for provider "alicloud" (hashicorp/alicloud) 1.90.1...
    ...
    
    You may now begin working with Terraform. Try running "terraform plan" to see
    any changes that are required for your infrastructure. All Terraform commands
    should now work.
    
    If you ever set or change modules or backend configuration for Terraform,
    rerun this command to reinitialize your working directory. If you forget, other
    commands will detect it and remind you to do so if necessary.
    ```

3.  执行以下命令生成资源规划。

    ```
    terraform plan
    ```

    输出：

    ```
    Refreshing Terraform state in-memory prior to plan...
    The refreshed state will be used to calculate this plan, but will not be
    persisted to local or remote state storage.
    ...
    Plan: 5 to add, 0 to change, 0 to destroy.
    ...
    ```

4.  执行以下命令创建集群。

    ```
    terraform apply
    ```

    输出：

    ```
    ...
    Do you want to perform these actions?
      Terraform will perform the actions described above.
      Only 'yes' will be accepted to approve.
    
      Enter a value: yes
    ...
    alicloud_cs_managed_kubernetes.default: Creation complete after 8m26s [id=************]
    
    Apply complete! Resources: 5 added, 0 changed, 0 destroyed.
    ```


## 使用Terraform删除ACK托管版集群

您可以执行`terraform destroy`命令删除通过Terraform创建的集群。

```
terraform destroy
```

输出：

```
...
Do you really want to destroy all resources?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes
...
Destroy complete! Resources: 5 destroyed.
```

**相关文档**  


[OpenAPI Explorer](https://api.alibabacloud.com/#/?product=Ons)

[阿里云容器服务Terraform资源](https://registry.terraform.io/providers/aliyun/alicloud/latest/docs/resources/cs_kubernetes)

[阿里云Terraform Provider](https://github.com/hashicorp/terraform-provider-alicloud)

[使用Terraform创建自动伸缩节点池](/intl.zh-CN/Kubernetes集群用户指南/节点管理/节点池管理/使用Terraform创建自动伸缩节点池.md)

