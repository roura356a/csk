---
keyword: [Terraform, auto-scaling node pool, Alibaba Cloud Provider, create]
---

# Use Terraform to create an auto-scaling node pool

Nodes in an auto-scaling node pool can automatically scale in or out. You can use Terraform to create an auto-scaling node pool. This topic describes how to use Terraform to create an auto-scaling node pool.

-   Terraform is installed. For more information, see [t41667.dita\#task\_bts\_tlz\_dfb]().
-   The auto scaling feature is dependent on Auto Scaling \(ESS\). Therefore, you must activate ESS and assign the ESS default role to your account before you enable auto scaling for nodes. For more information, see [Activate ESS](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/Auto scaling of nodes.mdstep_t9t_hu3_cbw).

    **Note:** If you have used the alicloud\_cs\_kubernetes\_autoscaler component, ESS is activated.

-   ESS-related policies are attached to your account in the RAM console. This grants your cluster the required permissions to access ESS. For more information about how to assign RAM policies, see [Authorization](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/Auto scaling of nodes.mdstep_dkz_59w_xec).

Terraform is an open source tool that supports new infrastructures through Terraform providers. You can use Terraform to preview, configure, and manage cloud infrastructures and resources. For more information, see [What is Terraform?]().

In the earlier versions of [Alibaba Cloud Provider](https://registry.terraform.io/providers/aliyun/alicloud/latest), Container Service for Kubernetes \(ACK\) provides a component named [alicloud\_cs\_kubernetes\_autoscaler](https://registry.terraform.io/providers/aliyun/alicloud/latest/docs/resources/cs_kubernetes_autoscaler). The alicloud\_cs\_kubernetes\_autoscaler component can be used to enable auto scaling for nodes. However, the following limits apply:

-   Complex configurations and high costs are required.
-   Each node to be scaled is added to the default node pool and cannot be separately maintained.
-   Some parameters cannot be modified.

Alibaba Terraform Provider 1.111.0 and later allow you to create auto-scaling node pools by using the [alicloud\_cs\_kubernetes\_node\_pool](https://registry.terraform.io/providers/aliyun/alicloud/latest/docs/resources/cs_kubernetes_node_pool) component. This component has the following benefits:

-   Provides simple scaling configurations. You only need to set the lower and upper limits of the node quantity in the scaling group.
-   Uses default settings for optional parameters to prevent inconsistent environments among nodes. This prevents user errors. For example, you may configure different operating system \(OS\) images for different nodes.
-   Allows you to explicitly view the changes of nodes in the auto-scaling node pool in the ACK console.

## Use Terraform to create an auto-scaling node pool

1.  If you have used the alicloud\_cs\_kubernetes\_autoscaler component, grant ESS-related RAM policies on your cluster and perform the following steps to switch the component to alicloud\_cs\_kubernetes\_node\_pool. Then, you can create auo-scaling node pools in your cluster.

**Note:** If you have not used the alicloud\_cs\_kubernetes\_autoscaler component, skip Step 1 and Step 2. Go to [Step 3](#step_r9b_l96_a5y).

2.  Modify the autoscaler-meta ConfigMap.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane of the ACK console, click **Clusters**.

    3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

    4.  In the left-side navigation pane of the details page, choose **Configurations** \> **ConfigMaps**.

    5.  In the upper-left corner of the ConfigMap page, select **kuber-system** from the **Namespace** drop-down list. Find the autoscaler-meta ConfigMap and click **Edit** in the **Actions** column.

    6.  In the Edit panel, modify the value of the **autoscaler-meta** ConfigMap.

        You need to change the value of `taints` from string type to array type. In this case, change `"taints":""` to `"taints":[]` in the **Value** text box.

    7.  Click **OK**.

3.  Synchronize the node pool.

    1.  In the left-side navigation pane of the details page, choose **Nodes** \> **Node Pools**.

    2.  In the upper-right corner of the Node Pools page, click **Sync Node Pool**.

4.  If you have not used the alicloud\_cs\_kubernetes\_autoscaler component, perform the following steps.
5.  Use Terraform to create an auto-scaling node pool.

    1.  Create the configuration file for an auto-scaling node pool.

        -   The following code provides an example on how to create an auto-scaling node pool in an existing ACK cluster.

            ```
            provider "alicloud" {
              access_key = ""
              secret_key = ""
              region     = "cn-hangzhou"
            }
            # Create an auto-scaling node pool in an existing ACK cluster.
            resource "alicloud_cs_kubernetes_node_pool" "at1" {
              # The ID of the ACK cluster where you want to create an auto-scaling node pool.
              cluster_id           = ""
              name                 = "np-test"
              # The vSwitches that are used by nodes in the node pool. You must specify at least one vSwitch.
              vswitch_ids          = ["vsw-bp1mdigyhmilu2h4v****"]
              instance_types       = ["ecs.e3.medium"]
              password             = "Hello1234"
             
              scaling_config {
                # The minimum number of nodes in the node pool.
                min_size     = 1
                # The maximum number of nodes in the node pool.
                max_size     = 5
              }
            
            }
            ```

        -   The following code provides an example on how to create a managed Kubernetes cluster and an auto-scaling node pool in the cluster.

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
            
            # Create a managed Kubernetes cluster.
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
            
            # Create an auto-scaling node pool in the cluster.
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

    2.  Run the `terraform apply` command to create the node pool.

    After the auto-scaling node pool is created, you can find the newly created node pool on the Node Pools page. **Auto Scaling Enabled** appears below the name of the node pool.


**Related topics**  


[Overview](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node pool management/Overview.md)

[Auto scaling of nodes](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/Auto scaling of nodes.md)

[Use Terraform to create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Use Terraform to create a managed Kubernetes cluster.md)

[alicloud\_cs\_kubernetes\_node\_pool](https://registry.terraform.io/providers/aliyun/alicloud/latest/docs/resources/cs_kubernetes_node_pool)

[alicloud\_cs\_kubernetes\_autoscaler](https://registry.terraform.io/providers/aliyun/alicloud/latest/docs/resources/cs_kubernetes_autoscaler)

