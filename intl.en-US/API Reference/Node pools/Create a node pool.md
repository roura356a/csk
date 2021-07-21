# Create a node pool

You can call the CreateClusterNodePool operation to create a node pool for a Container Service for Kubernetes \(ACK\) cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=CreateClusterNodePool&type=ROA&version=2015-12-15)

## Request syntax

```
POST /clusters/ClusterId/nodepools HTTP/1.1 
Content-Type:application/json
{
  "auto_scaling" : {
    "enable" : Boolean,
    "max_instances" : Long,
    "min_instances" : Long,
    "type" : "String",
    "is_bond_eip" : Boolean,
    "eip_internet_charge_type" : "String",
    "eip_bandwidth" : Long
  },
  "kubernetes_config" : {
    "cms_enabled" : Boolean,
    "cpu_policy" : "String",
    "labels" : [ {
      "key" : "String",
      "value" : "String"
    } ],
    "runtime" : "String",
    "runtime_version" : "String",
    "taints" : [ {
      "key" : "String",
      "value" : "String",
      "effect" : "String"
    } ],
    "user_data" : "String"
  },
  "nodepool_info" : {
    "name" : "String",
    "resource_group_id" : "String"
  },
  "scaling_group" : {
    "auto_renew" : Boolean,
    "auto_renew_period" : Long,
    "data_disks" : [ {
      "category" : "String",
      "size" : Long,
      "encrypted" : "String",
      "auto_snapshot_policy_id" : "String"
    } ],
    "image_id" : "String",
    "instance_charge_type" : "String",
    "instance_types" : [ "String" ],
    "key_pair" : "String",
    "login_password" : "String",
    "period" : Long,
    "period_unit" : "String",
    "platform" : "String",
    "rds_instances" : [ "String" ],
    "spot_strategy" : "String",
    "spot_price_limit" : [ {
      "instance_type" : "String",
      "price_limit" : "String"
    } ],
    "scaling_policy" : "String",
    "security_group_id" : "String",
    "system_disk_category" : "String",
    "system_disk_size" : Long,
    "tags" : [ {
      "key" : "String",
      "value" : "String"
    } ],
    "vswitch_ids" : [ "String" ],
    "multi_az_policy" : "String",
    "on_demand_base_capacity" : Long,
    "on_demand_percentage_above_base_capacity" : Long,
    "spot_instance_pools" : Long,
    "spot_instance_remedy" : Boolean,
    "compensate_with_on_demand" : Boolean
  },
  "tee_config" : {
    "tee_enable" : Boolean
  },
  "management" : {
    "enable" : Boolean,
    "auto_repair" : Boolean,
    "upgrade_config" : {
      "auto_upgrade" : Boolean,
      "surge" : Long,
      "surge_percentage" : Long,
      "max_unavailable" : Long
    }
  },
  "count" : Long
}
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|c61da77e8bfbc4c4c999af2b51b65\*\*\*\*|The ID of the ACK cluster. |

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|auto\_scaling|Object|No| |The configuration of auto scaling. |
|enable|Boolean|Yes|true|Specifies whether to enable auto scaling. Valid values:

 -   `true`: enables auto scaling.
-   `false`: disables auto scaling. If you set this parameter to false, other parameters in the `auto_scaling` section do not take effect.

 Default value: `false`. |
|max\_instances|Long|Yes|10|The maximum number of Elastic Compute Service \(ECS\) instances in the scaling group. |
|min\_instances|Long|Yes|1|The minimum number of ECS instances in the scaling group. |
|type|String|No|cpu|The instance types that can be used for the auto scaling of the node pool. Valid values:

 -   `cpu`: regular instance.
-   `gpu`: GPU-accelerated instance.
-   `gpushare`: shared GPU-accelerated instance.
-   `spot`: preemptible instance.

 Default value: `cpu`. |
|is\_bond\_eip|Boolean|No|true|Specifies whether to associate an elastic IP address \(EIP\) with the node pool. Valid values:

 -   `true`: associates an EIP with the node pool.
-   `false`: does not associate an EIP with the node pool.

 Default value: `false`. |
|eip\_internet\_charge\_type|String|No|PayByBandwidth|The billing method of the EIP. Valid values:

 -   `PayByBandwidth`: pay-by-bandwidth.
-   `PayByTraffic`: pay-by-data-transfer.

 Default value: `PayByBandwidth`. |
|eip\_bandwidth|Long|No|5|The peak bandwidth of the EIP. |
|kubernetes\_config|Object|No| |The configurations of the ACK cluster. |
|cms\_enabled|Boolean|No|true|Specifies whether to install the CloudMonitor agent on ECS nodes. After the CloudMonitor agent is installed on ECS nodes, you can view monitoring information about the instances in the CloudMonitor console. We recommend that you install the CloudMonitor agent. Valid values:

 -   `true`: installs the CloudMonitor agent on ECS nodes.
-   `false`: does not install the CloudMonitor agent on ECS nodes

 Default value: `false`. |
|cpu\_policy|String|No|none|The CPU policy. The following policies are supported if the Kubernetes version of the cluster is 1.12.6 or later.

 -   `static`: This policy allows pods with specific resource characteristics on the node to be granted with enhanced CPU affinity and exclusivity.
-   `none`: This policy indicates that the default CPU affinity is used.

 Default value: `none`. |
|labels|Array of [tag](/intl.en-US/API Reference/Commonly used parameters.md)|No| |You can add labels to nodes that are added to the cluster. |
|runtime|String|Yes|docker|The name of the container runtime. |
|runtime\_version|String|Yes|19.03.5|The version of the container runtime. |
|taints|Array of [taint](/intl.en-US/API Reference/Commonly used parameters.md)|No| |The taints of the nodes. |
|user\_data|String|No|dGhpcyBpcyBhIGV4YW1wbGU=|The user-defined data. |
|nodepool\_info|Object|No| |The configurations of the node pool. |
|name|String|Yes|cluster-demo|The name of the node pool. |
|resource\_group\_id|String|No|rg-acfmyvw3wjmb\*\*\*\*|The ID of the resource group to which the node pool belongs. |
|scaling\_group|Object|No| |The configurations of the scaling group used by the node pool. |
|auto\_renew|Boolean|No|true|Specifies whether to enable auto-renewal for nodes in the node pool. This parameter takes effect only when `instance_charge_type` is set to `PrePaid`. Valid values:

 -   `true`: enables auto-renewal.
-   `false`: disables auto-renewal.

 Default value: `true`. |
|auto\_renew\_period|Long|No|1|The auto-renewal period for nodes in the node pool. This parameter takes effect and is required only when instance\_charge\_type is set to PrePaid and auto\_renew is set to true. If `PeriodUnit=Month` is configured, the valid values are: 1, 2, 3, 6, and 12.

 Default value: 1 |
|data\_disks|Array of [data\_disk](/intl.en-US/API Reference/Commonly used parameters.md)|No| |The configurations of data disks. |
|image\_id|String|No|aliyun\_2\_1903\_x64\_20G\_alibase\_20200529.vhd|The ID of a custom image. By default, the image provided by ACK is used. |
|instance\_charge\_type|String|Yes|PrePaid|The billing method of nodes in the node pool. Valid values:

 -   `PrePaid`: subscription.
-   `PostPaid`: pay-as-you-go.

 Default value: `PostPaid`. |
|instance\_types|Array of String|Yes|ecs.d1ne.2xlarge|The ECS instance types of the nodes. |
|key\_pair|String|No|np-key-name|The name of the key pair used to log on to the nodes. You must set key\_pair or `login_password`.

 **Note:** **Note**: If you want to create a managed node pool, you must set `key_pair`. |
|login\_password|String|No|Hello1234|The password used to log on to the nodes by using SSH. You must set `key_pair` or login\_password. The password must be 8 to 30 characters in length, and must contain at least three of the following character types: uppercase letters, lowercase letters, digits, and special characters. |
|period|Long|No|1|The subscription period of nodes in the node pool. This parameter takes effect and is required only when instance\_charge\_type is set to

`PrePaid`.

If `PeriodUnit=Month` is configured, Period

`the valid values are: 1, 2, 3, 6, and 12. Default value: 1.`|
|period\_unit|String|No|Month|The unit of the subscription period of nodes in the node pool. This parameter is required if you set instance\_charge\_type to `PrePaid`. A value of Month specifies that the subscription period is measured in months. |
|platform|String|Yes|Linux|The release version of the operating system. Valid values:

 -   `CentOS`
-   `AliyunLinux`
-   `Windows`
-   `WindowsCore`

 Default value: `AliyunLinux`. |
|rds\_instances|Array of String|No|rds-\*\*\*\*|The IDs of the ApsaraDB RDS instances. |
|spot\_strategy|String|No|NoSpot|The type of preemptible instance. Valid values:

 -   `NoSpot`: non-preemptible instance.
-   `SpotWithPriceLimit`: specifies the highest bid for a preemptible instance.
-   `SpotAsPriceGo`: automatically submits bids based on the up-to-date market price.

 For more information, see [Preemptible instances](~~165053~~). |
|spot\_price\_limit|Array|No| |The instance type for preemptible instances and the price limit of the instance type. |
|instance\_type|String|No|ecs.c6.large|The instance type for preemptible instances. |
|price\_limit|String|No|0.39|The price limit of a preemptible instance. Unit: USD/hour. |
|scaling\_policy|String|No|release|The scaling mode of the scaling group. Valid values:

 -   `release`: the standard mode. ECS instances are created and released based on the resource usage.
-   `recycle`: the swift mode. ECS instances are created, stopped, or started during scaling events. This reduces the time required for the next scale-out event. When the instance is stopped, you are charged only for the storage service. This does not apply to ECS instances attached with local disks.

 Default value:`release`. |
|security\_group\_id|String|Yes|sg-wz9a8g2mt6x5llu0\*\*\*\*|The ID of the security group to which the nodes belong. |
|system\_disk\_category|String|Yes|cloud\_efficiency|The type of system disk. Valid values:

 -   `cloud_efficiency`: ultra disk.
-   `cloud_ssd`: standard SSD.
-   `cloud_essd`: enhanced SSD.

 Default value: `cloud_efficiency`. |
|system\_disk\_size|Long|Yes|120|The system disk size of a node. Unit: GiB.

 Valid values: 40 to 500. |
|tags|Array|No| |Adds labels only to ECS instances.

 A key must be unique and cannot exceed 128 characters in length. Neither keys nor values can start with aliyun or acs:. Neither keys nor values can contain https:// or http://. |
|key|String|No|node-k-1|The name of the label. |
|value|String|No|node-v-1|The value of the label. |
|vswitch\_ids|Array of String|Yes|vsw-wz9mfnhmssud6eicu\*\*\*\*|The IDs of vSwitches. |
|multi\_az\_policy|String|No|COST\_OPTIMIZED|The scaling policy of ECS instances in a multi-zone scaling group. Valid values:

 -   `PRIORITY`: the scaling group is scaled based on the VSwitchIds.N parameter. When ECS instances cannot be created in the zone where the vSwitch with the highest priority is deployed, the system attempts to create ECS instances in the zone where the vSwitch with the next highest priority is deployed.
-   `COST_OPTIMIZED`: ECS instances are created based on the unit price of vCPUs in ascending order. Preemptible instances are preferably created when multiple instance types are specified in the scaling configurations. You can set the `CompensateWithOnDemand` parameter to specify whether to automatically create pay-as-you-go instances when preemptible instances cannot be created due to insufficient inventory.

**Note:** ****`COST_OPTIMIZED` takes effect only when multiple instance types are specified or at least one instance type is specified for preemptible instances.

-   `BALANCE`: ECS instances are evenly distributed across multiple zones specified by the scaling group. If ECS instances are not evenly distributed across multiple zones due to insufficient inventory, you can call the [RebalanceInstances](~~71516~~) operation to balance the instance distribution among zones.

 Default value: `PRIORITY`. |
|on\_demand\_base\_capacity|Long|No|0|The minimum number of pay-as-you-go instances that must be kept in the scaling group. Valid values: 0 to 1000. When the number of pay-as-you-go instances is lower than this value, pay-as-you-go instances are preferably created to meet the required number. |
|on\_demand\_percentage\_above\_base\_capacity|Long|No|20|The percentage of pay-as-you-go instances among the extra instances that exceed the number specified by `on_demand_base_capacity`. Valid values: 0 to 100. |
|spot\_instance\_pools|Long|No|5|The number of available instance types. The scaling group creates preemptible instances of multiple instance types at the lowest cost. Valid values: 1 to 10. |
|spot\_instance\_remedy|Boolean|No|false|Specifies whether to supplement preemptible instances. If this parameter is set to true, when the scaling group receives a system message that a preemptible instance is to be reclaimed, the scaling group attempts to create a new instance to replace this instance. Valid values:

 -   `true`: supplements preemptible instances.
-   `false`: does not supplement preemptible instances. |
|compensate\_with\_on\_demand|Boolean|No|true|Specifies whether to automatically create pay-as-you-go instances to meet the required number of ECS instances if preemptible instances cannot be created due to reasons such as the cost or insufficient inventory. This parameter takes effect when `multi_az_policy` is set to `COST_OPTIMIZED`. Valid values:

 -   `true`: automatically creates pay-as-you-go instances to meet the required number of ECS instances if preemptible instances cannot be created.
-   `false`: does not create pay-as-you-go instances to meet the required number of ECS instances if preemptible instances cannot be created. |
|tee\_config|Object|No| |The configurations of confidential computing. |
|tee\_enable|Boolean|Yes|true|Specifies whether to enable confidential computing for the cluster. |
|management|Object|No| |The configurations of the managed node pool. |
|enable|Boolean|Yes|false|Specifies whether to enable managed node pools. Valid values:

 -   `true`: enables managed node pools.
-   `false`: disables managed node pools. The other parameters in this section take effect only when enable=true is specified. |
|auto\_repair|Boolean|No|false|Specifies whether to enable auto repairing. This parameter takes effect only when `enable=true` is specified.

 -   `true`: enables auto repairing.
-   `false`: disables auto repairing. |
|upgrade\_config|Object|No| |The configurations of auto upgrading. The configurations take effect only when `enable=true` is specified. |
|auto\_upgrade|Boolean|No|false|Specifies whether to enable auto upgrading. Valid values:

 -   `true`: enables auto upgrading.
-   `false`: disables auto upgrading. |
|surge|Long|No|0|The number of extra nodes that can be added to the node pool during an auto upgrade. |
|surge\_percentage|Long|No|0|The ratio of extra nodes to the nodes in the node pool. You must set this parameter or `surge`. |
|max\_unavailable|Long|Yes|0|The maximum number of nodes that can be in the unschedulable state.

 Valid values: 1 to 1000.

 Default value: 1. |
|count|Long|No|1|The number of nodes in the node pool. |

## Response syntax

```
HTTP/1.1 200
Content-Type:application/json
{
  "nodepool_id" : "String"
}
```

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|nodepool\_id|String|np31da1b38983f4511b490fc62108a\*\*\*\*|The ID of the created node pool. |

## Examples

Sample requests

```
POST /clusters/c61da77e8bfbc4c4c999af2b51b65****/nodepools HTTP/1.1 
Content-Type:application/json
{
  "auto_scaling" : {
    "enable" : true,
    "max_instances" : 10,
    "min_instances" : 1,
    "type" : "cpu",
    "is_bond_eip" : true,
    "eip_internet_charge_type" : "PayByBandwidth",
    "eip_bandwidth" : 5
  },
  "kubernetes_config" : {
    "cms_enabled" : true,
    "cpu_policy" : "none",
    "labels" : [ {
      "key" : "env",
      "value" : "prod"
    } ],
    "runtime" : "docker",
    "runtime_version" : "19.03.5",
    "taints" : [ {
      "key" : "key",
      "value" : "value",
      "effect" : "NoSchedule"
    } ],
    "user_data" : "dGhpcyBpcyBhIGV4YW1wbGU="
  },
  "nodepool_info" : {
    "name" : "cluster-demo",
    "resource_group_id" : "rg-acfmyvw3wjmb****"
  },
  "scaling_group" : {
    "auto_renew" : true,
    "auto_renew_period" : 1,
    "data_disks" : [ {
      "category" : "cloud_ssd",
      "size" : 40,
      "encrypted" : "true",
      "auto_snapshot_policy_id" : "sp-2zej1nogjvovnz4z****"
    } ],
    "image_id" : "aliyun_2_1903_x64_20G_alibase_20200529.vhd",
    "instance_charge_type" : "PrePaid",
    "instance_types" : [ "ecs.d1ne.2xlarge" ],
    "key_pair" : "np-key-name",
    "login_password" : "Hello1234",
    "period" : 1,
    "period_unit" : "Month",
    "platform" : "Linux",
    "rds_instances" : [ "rds-***" ],
    "spot_strategy" : "NoSpot",
    "spot_price_limit" : [ {
      "instance_type" : "ecs.c6.large",
      "price_limit" : "0.39"
    } ],
    "scaling_policy" : "release",
    "security_group_id" : "sg-wz9a8g2mt6x5llu0****",
    "system_disk_category" : "cloud_efficiency",
    "system_disk_size" : 120,
    "tags" : [ {
      "key" : "node-k-1",
      "value" : "node-v-1"
    } ],
    "vswitch_ids" : [ "vsw-wz9mfnhmssud6eicu****" ],
    "multi_az_policy" : "COST_OPTIMIZED",
    "on_demand_base_capacity" : 0,
    "on_demand_percentage_above_base_capacity" : 20,
    "spot_instance_pools" : 5,
    "spot_instance_remedy" : false,
    "compensate_with_on_demand" : true
  },
  "tee_config" : {
    "tee_enable" : true
  },
  "management" : {
    "enable" : false,
    "auto_repair" : false,
    "upgrade_config" : {
      "auto_upgrade" : false,
      "surge" : 0,
      "surge_percentage" : 0,
      "max_unavailable" : 0
    }
  },
  "count" : 1
}
```

Description of the sample request

```
Create a node pool that has auto scaling enabled:
{
    "ClusterId":"c61da77e8bfbc4c4c999af2b51b65****",
    "nodepool_info":{
        "name":"autoScale-demo",
        "resource_group_id":"rg-acfmyvw3wjm****"
    },
    "scaling_group":{
        "vswitch_ids":[
            "vsw-wz9mfnhmssud6eic****"
        ],
        "system_disk_category":"cloud_efficiency",
        "system_disk_size":120,
        "data_disks":[

        ],
        "instance_types":[
            "ecs.t6-c1m2.large"
        ],
        "vpc_id":"vpc-wz984yvbd6lck22z3****",
        "tags":[

        ],
        "instance_charge_type":"PostPaid",
        "login_password":"****",
        "platform":"AliyunLinux",
        "image_id":"aliyun_2_1903_x64_20G_alibase_20200529.vhd",
        "rds_instances":[

        ],
        "scaling_policy":"release"
    },
    "kubernetes_config":{
        "cpu_policy":"none",
        "cms_enabled":false,
        "labels":[
            {
                "key":"workload_type",
                "value":"cpu"
            }
        ],
        "taints":[

        ],
        "user_data":"",
        "runtime":"docker",
        "runtime_version":"19.03.5"
    },
    "tee_config":{
        "tee_enable":true
    },
    "auto_scaling":{
        "enable":true,
        "max_instances":10,
        "min_instances":1,
        "type":"cpu"
    }
}
```

Sample success responses

`XML` format

```
HTTP/1.1 200 OK
Content-Type:application/xml

<nodepool_id>np31da1b38983f4511b490fc62108a****</nodepool_id>
```

`JSON` format

```
HTTP/1.1 200 OK
Content-Type:application/json

{
  "nodepool_id" : "np31da1b38983f4511b490fc62108a****"
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

