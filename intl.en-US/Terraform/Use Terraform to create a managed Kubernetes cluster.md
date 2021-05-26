---
keyword: [Terraform, managed Kubernetes cluster]
---

# Use Terraform to create a managed Kubernetes cluster

Terraform is an open source tool provided by HashiCorp. Terraform enables you to securely and efficiently preview, configure, and manage cloud infrastructures and resources. You can use Terraform to automatically create and update Alibaba Cloud infrastructures and resources, and implement version management based on your requirements. This topic describes how to create a managed Kubernetes cluster by using Terraform.

-   Terraform is installed.

    **Note:** You must install Terraform 0.12.28 or later. You can run the `terraform --version` command to query the Terraform version.

    -   By default, Cloud Shell is preinstalled with Terraform and configured with your account information. You do not need to make any configuration change.
    -   If you do not use Cloud Shell, you can directly install Terraform. For more information, see [Install and configure Terraform in the local PC]().
-   Your account information is configured. Environment variables are created to store identity information.

    ```
    export ALICLOUD_ACCESS_KEY="************"
    export ALICLOUD_SECRET_KEY="************"
    export ALICLOUD_REGION="cn-beijing"
    ```

    **Note:** To improve the flexibility and security of permission management, we recommend that you create a Resource Access Management \(RAM\) user named Terraform. Then, create an AccessKey pair for the RAM user and grant permissions to the RAM user. For more information, see [Create a RAM user](/intl.en-US/RAM User Management/Basic operations/Create a RAM user.md) and [Grant permissions to a RAM user](/intl.en-US/RAM User Management/Authorization management/Grant permissions to a RAM user.md).


## Use Terraform to create a managed Kubernetes cluster

1.  Create a working directory and a file named main.tf under the directory.

    The main.tf file is used to configure the following settings for Terraform:

    -   Create a new virtual private cloud \(VPC\) and vSwitches in the VPC.
    -   Create a managed Kubernetes cluster that contains two worker nodes.
    ```
    provider "alicloud" {
    }
    variable "k8s_name_prefix" {
      description = "The name prefix used to create managed kubernetes cluster."
      default     = "tf-ack"
    }
    resource "random_uuid" "this" {}
    # The default resource names.
    locals {
      k8s_name     = substr(join("-", [var.k8s_name_prefix, random_uuid.this.result]), 0, 63)
      new_vpc_name = "vpc-for-${local.k8s_name}"
      new_vsw_name = "vsw-for-${local.k8s_name}"
      log_project_name = "log-for-${local.k8s_name}"
    }
    # The Elastic Compute Service (ECS) instance specifications of the worker nodes.
    data "alicloud_instance_types" "default" {
      cpu_core_count       = 4
      memory_size          = 8
      kubernetes_node_role = "Worker"
    }
    // The zone that has sufficient ECS instances of the required specifications.
    data "alicloud_zones" "default" {
      available_instance_type = data.alicloud_instance_types.default.instance_types[0].id
    }
    # The VPC.
    resource "alicloud_vpc" "default" {
      name       = local.new_vpc_name
      cidr_block = "10.1.0.0/21"
    }
    # The vSwitches.
    resource "alicloud_vswitch" "vswitches" {
      name              = local.new_vsw_name
      vpc_id            = alicloud_vpc.default.id
      cidr_block        = "10.1.1.0/24"
      availability_zone = data.alicloud_zones.default.zones[0].id
    }
    # The Log Service project.
    resource "alicloud_log_project" "log" {
      name        = local.log_project_name
      description = "created by terraform for managedkubernetes cluster"
    }
    # The managed Kubernetes cluster.
    resource "alicloud_cs_managed_kubernetes" "default" {
      # The name of the cluster.
      name                      = local.k8s_name
      # The vSwitches of the new Kubernetes cluster. Specify the IDs of one or more vSwitches. The vSwitches must be in the zone specified by availability_zone.
      worker_vswitch_ids        = split(",", join(",", alicloud_vswitch.vswitches. *.id))
      # Specify whether to create a new Network Address Translation (NAT) gateway when the Kubernetes cluster is created. Default value: true.
      new_nat_gateway           = true
      # The ECS instance types of the worker nodes.
      worker_instance_types     = [data.alicloud_instance_types.default.instance_types[0].id]
      # The total number of worker nodes in the Kubernetes cluster. Default value: 3. Maximum value: 50.
      worker_number             = 2
      # The password that is used to log on to the nodes through SSH.
      password                  = "Yourpassword1234"
      # The CIDR block of the pods. When cluster_network_type is set to flannel, you must set this parameter. It cannot be the same as the CIDR block of the VPC or the CIDR blocks of the Kubernetes clusters in the VPC. It cannot be modified after the cluster is created. Maximum number of hosts in the cluster: 256.
      pod_cidr                  = "172.20.0.0/16"
      # The CIDR block of the Services. It cannot be the same as the CIDR block of the VPC or the CIDR blocks of the Kubernetes clusters in the VPC. It cannot be modified after the cluster is created.
      service_cidr              = "172.21.0.0/20"
      # Specify whether to install the Cloud Monitor agent on nodes.
      install_cloud_monitor     = true
      # Specify whether to create an Internet-facing Server Load Balancer (SLB) instance for the API server. Default value: false.
      slb_internet_enabled      = true
      # The type of the system disks for worker nodes. Valid values: cloud_ssd and cloud_efficiency. Default value: cloud_efficiency.
      worker_disk_category      = "cloud_efficiency"
      # The type of the data disks for worker nodes. Valid values: cloud_ssd and cloud_efficiency. If you do not specify this parameter, no data disk will be created.
      worker_data_disk_category = "cloud_ssd"
      # The size of each data disk for each worker node. Valid values: 20 to 32768. Unit: GB. Default value: 40.
      worker_data_disk_size     = 200
      # Logging configuration.
      addons {
        name     = "logtail-ds"
        config   = "{\"IngressDashboardEnabled\":\"true\",\"sls_project_name\":alicloud_log_project.log.name}"
      }
    }
    ```

2.  Run the following command to initialize the environment for Terraform.

    ```
    terraform init
    ```

    The following output is returned:

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

3.  Run the following command to create an execution plan.

    ```
    terraform plan
    ```

    The following output is returned:

    ```
    Refreshing Terraform state in-memory prior to plan...
    The refreshed state will be used to calculate this plan, but will not be
    persisted to local or remote state storage.
    ...
    Plan: 5 to add, 0 to change, 0 to destroy.
    ...
    ```

4.  Run the following command to create the cluster.

    ```
    terraform apply
    ```

    The following output is returned:

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


## Use Terraform to delete a managed Kubernetes cluster

You can run the `terraform destroy` command to delete a managed Kubernetes cluster that is created by using Terraform.

```
terraform destroy
```

The following output is returned:

```
...
Do you really want to destroy all resources?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes
...
Destroy complete! Resources: 5 destroyed.
```

**Related topics**  


[Alibaba Cloud OpenAPI Explorer](https://api.aliyun.com/#/cli?tool=Terraform&module=managed-kubernetes&namespace=terraform-alicloud-modules)

[Terraform resources for ACK](https://registry.terraform.io/providers/aliyun/alicloud/latest/docs/resources/cs_kubernetes)

[Terraform provider for Alibaba Cloud](https://github.com/hashicorp/terraform-provider-alicloud)

[Use Terraform to create an auto-scaling node pool](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node pool management/Use Terraform to create an auto-scaling node pool.md)

