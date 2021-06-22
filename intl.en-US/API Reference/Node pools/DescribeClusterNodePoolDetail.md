# DescribeClusterNodePoolDetail

Queries the details of a specified node pool.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DescribeClusterNodePoolDetail&type=ROA&version=2015-12-15)

## Request syntax

```
GET /clusters/ClusterId/nodepools/NodepoolId HTTP/1.1 
Content-Type:application/json
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|c23421cfa74454bc8b37163fd19af\*\*\*\*|The ID of the Container Service for Kubernetes \(ACK\) cluster that you want to manage. |
|NodepoolId|String|Yes|np31da1b38983f4511b490fc62108a\*\*\*\*|The ID of the node pool that you want to query. |

## Response syntax

```
HTTP/1.1 200
Content-Type:application/json
{
  "auto_scaling" : {
    "eip_bandwidth" : Long,
    "eip_internet_charge_type" : "String",
    "enable" : Boolean,
    "is_bond_eip" : Boolean,
    "max_instances" : Long,
    "min_instances" : Long,
    "type" : "String"
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
    "created" : "String",
    "is_default" : Boolean,
    "name" : "String",
    "nodepool_id" : "String",
    "region_id" : "String",
    "resource_group_id" : "String",
    "type" : "String",
    "updated" : "String"
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
    "multi_az_policy" : "String",
    "on_demand_base_capacity" : Long,
    "on_demand_percentage_above_base_capacity" : Long,
    "spot_instance_pools" : Long,
    "spot_instance_remedy" : Boolean,
    "compensate_with_on_demand" : Boolean,
    "period" : Long,
    "period_unit" : "String",
    "platform" : "String",
    "ram_policy" : "String",
    "spot_strategy" : "String",
    "spot_price_limit" : [ {
      "instance_type" : "String",
      "price_limit" : "String"
    } ],
    "rds_instances" : [ "String" ],
    "scaling_group_id" : "String",
    "scaling_policy" : "String",
    "security_group_id" : "String",
    "system_disk_category" : "String",
    "system_disk_size" : Long,
    "tags" : [ {
      "key" : "String",
      "value" : "String"
    } ],
    "vswitch_ids" : [ "String" ],
    "login_password" : "String",
    "key_pair" : "String"
  },
  "status" : {
    "failed_nodes" : Long,
    "healthy_nodes" : Long,
    "initial_nodes" : Long,
    "offline_nodes" : Long,
    "removing_nodes" : Long,
    "serving_nodes" : Long,
    "state" : "String",
    "total_nodes" : Long
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
  }
}
```

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|auto\_scaling|object| |The auto scaling configurations of the queried node pool. |
|eip\_bandwidth|Long|5|The peak bandwidth of the elastic IP address \(EIP\) associated with the node pool. |
|eip\_internet\_charge\_type|String|PayByBandwidth|The billing method of the EIP. Valid values:

 -   `PayByBandwidth`: pay-by-bandwidth.
-   `PayByTraffic`: pay-by-data-transfer. |
|enable|Boolean|true|Indicates whether auto scaling is enabled.

 -   `true`: enables auto scaling.
-   `false`: disables auto scaling. If this parameter is set to false, the other parameters in the `auto_scaling` section do not take effect. |
|is\_bond\_eip|Boolean|true|Indicates whether an EIP is associated with the node pool. Valid values:

 -   `true`: An EIP is associated with the node pool.
-   `false`: No EIP is associated with the node pool. |
|max\_instances|Long|10|The maximum number of Elastic Compute Service \(ECS\) instances supported by the node pool. |
|min\_instances|Long|1|The minimum number of ECS instances supported by the node pool. |
|type|String|cpu|The instance types that can be used for the auto scaling of the node pool. Valid values:

 -   `cpu`: regular instance.
-   `gpu`: GPU-accelerated instance.
-   `gpushare`: shared GPU-accelerated instance.
-   `spot`: preemptible instance. |
|kubernetes\_config|object| |The configurations of the ACK cluster where the node pool is deployed. |
|cms\_enabled|Boolean|true|Indicates where the CloudMonitor agent is installed on ECS nodes of the cluster. After the CloudMonitor agent is installed, you can view monitoring information about the ECS instances in the CloudMonitor console. Installation is recommended. Valid values:

 -   `true`: The CloudMonitor agent is installed on ECS nodes.
-   `false`: The CloudMonitor agent is not installed on ECS nodes. |
|cpu\_policy|String|none|The CPU policy. The following policies are supported if the Kubernetes version of the cluster is V1.12.6 or later.

 -   `static`: This policy allows pods with specific resource characteristics on the node to be granted enhanced CPU affinity and exclusivity.
-   `none`: This policy indicates that the default CPU affinity is used. |
|labels|Array of [tag](/intl.en-US/API Reference/Commonly used parameters.md)| |You can add labels to the nodes in the cluster. Labeling rules:

 -   Each label is a case-sensitive key-value pair. You can add up to 20 labels.
-   A key must be unique and cannot exceed 64 characters in length. A value can be empty and cannot exceed 128 characters in length. Neither keys nor values can start with `aliyun`, `acs:`, `https://`, or `http://`. For more information, see [Labels and Selectors](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set). |
|runtime|String|docker|The name of the container runtime. |
|runtime\_version|String|19.03.5|The version of the container runtime. |
|taints|Array of [taint](/intl.en-US/API Reference/Commonly used parameters.md)| |The taints of the nodes in the node pool. Taints are added to nodes to prevent pods from being scheduled to inappropriate nodes. However, tolerations allow pods to be scheduled to nodes with matching taints. For more information, see [taint-and-toleration](https://kubernetes.io/zh/docs/concepts/scheduling-eviction/taint-and-toleration/). |
|user\_data|String|IyEvYmluL3NoCmVjaG8gIkhlbGxvIEFDSyEi|The user-defined data of the node pool. For more information, see [Prepare user-defined data](~~49121~~). |
|nodepool\_info|object| |The configurations of the node pool. |
|created|String|2020-09-27T19:14:09.156823496+08:00|The time when the node pool was created. |
|is\_default|Boolean|true|Indicates whether the node pool is a default node pool. An ACK cluster usually has only one default node pool. Valid values:

 `true`: The node pool is a default node pool.

 `false`: The node pool is not a default node pool. |
|name|String|default-nodepool|The name of the node pool.

 The name must be 1 to 63 characters in length, and can contain digits, letters, and hyphens \(-\). It cannot start with a hyphen. |
|nodepool\_id|String|np615c0e0966124216a0412e10afe0\*\*\*\*|The ID of the node pool. |
|region\_id|String|cn-beijing|The ID of the region where the ACK cluster is deployed. |
|resource\_group\_id|String|rg-acfmyvw3wjmb\*\*\*\*|The ID of the resource group to which the ACK cluster belongs. |
|type|String|ess|The type of the node pool. |
|updated|String|2020-09-27T20:37:46+08:00|The time when the node pool was updated. |
|scaling\_group|object| |The configurations of the scaling group. |
|auto\_renew|Boolean|false|Indicates whether auto-renewal is enabled for the nodes in the node pool. This parameter takes effect only when `instance_charge_type` is set to `PrePaid`. Valid values:

 -   `true`: Auto-renewal is enabled.
-   `false`: Auto-renewal is disabled. |
|auto\_renew\_period|Long|1|The duration of the auto-renewal. This parameter takes effect and is required only when `instance_charge_type` is set to `PrePaid`.

 If `PeriodUnit=Month` is configured, the valid values are: 1, 2, 3, 6, and 12. |
|data\_disks|Array of [data\_disk](/intl.en-US/API Reference/Commonly used parameters.md)| |The configurations of the data disks attached to the nodes in the node pool. The configurations include the disk type and disk size. |
|image\_id|String|aliyun\_2\_1903\_x64\_20G\_alibase\_20200529.vhd|The ID of the custom image. You can call `DescribeKubernetesVersionMetadata` to query the supported images. |
|instance\_charge\_type|String|PostPaid|The billing method of the nodes in the node pool. Valid values:

 -   `PrePaid`: subscription.
-   `PostPaid`: pay-as-you-go. |
|instance\_types|Array of String|ecs.n4.large|The instance types of the nodes in the node pool. |
|multi\_az\_policy|String|BALANCE|The scaling policy for the multi-zone scaling group that contains ECS instances. Valid values:

 -   `PRIORITY`: the scaling group is scaled based on the VSwitchIds.N parameter. When ECS instances cannot be created in the zone where the vSwitch with the highest priority is deployed, the system attempts to create ECS instances in the zone where the vSwitch with the next highest priority is deployed.
-   `COST_OPTIMIZED`: ECS instances are created based on the unit price of vCPUs in ascending order. Preemptible instances are preferentially created when multiple preemptible instance types are specified for the scaling configurations. You can set the `CompensateWithOnDemand` parameter to specify whether to automatically create pay-as-you-go instances when preemptible instances cannot be created due to insufficient inventory.

**Note:** `COST_OPTIMIZED` is valid only when multiple instance types are specified or at least one preemptible instance type is specified.

-   `BALANCE`: ECS instances are evenly distributed in multiple zones specified in the scaling group. If ECS instances become imbalanced among multiple zones due to insufficient inventory, you can call RebalanceInstances of Auto Scaling \(ESS\) to balance the instance distribution among zones. For more information, see [RebalanceInstances](~~71516~~).

 Default value: `PRIORITY`. |
|on\_demand\_base\_capacity|Long|0|The minimum number of pay-as-you-go instances that must be kept in the scaling group. Valid values: 0 to 1000. When the number of pay-as-you-go instances is less than this value, ESS preferentially creates pay-as-you-go instances to meet the required number. |
|on\_demand\_percentage\_above\_base\_capacity|Long|20|The percentage of pay-as-you-go instances in the extra instances beyond the number specified by `on_demand_base_capacity`. Valid values: 0 to 100. |
|spot\_instance\_pools|Long|5|The number of available instance types. ESS creates preemptible instances of multiple instance types at the lowest cost. Valid values: 1 to 10. |
|spot\_instance\_remedy|Boolean|false|Indicates whether to supplement preemptible instances when the number of preemptible instances drops below the specified minimum number. If this parameter is set to true, when the scaling group receives a system message that a preemptible instance is to be reclaimed, ESS attempts to create a new instance to replace this instance. Valid values:

 -   `true`: Supplement to preemptible instances is enabled.
-   `false`: Supplement to preemptible instances is disabled. |
|compensate\_with\_on\_demand|Boolean|true|Indicates whether to automatically create pay-as-you-go instances to meet the required number of ECS instances when the preemptible instances cannot be created due to reasons such as the cost or inventory availability. This parameter takes effect when `multi_az_policy` is set to `COST_OPTIMIZED`. Valid values:

 -   `true`: pay-as-you-go instances are automatically created to meet the required number of ECS instances when the preemptible instances cannot be created.
-   `false`: pay-as-you-go instances are not created to meet the required number of ECS instances when the preemptible instances cannot be created. |
|period|Long|0|The subscription duration of worker nodes. This parameter takes effect and is required only when `instance_charge_type` is set to `PrePaid`.

 If `PeriodUnit=Month` is specified, the valid values are: 1, 2, 3, 6, 12, 24, 36, 48, and 60. |
|period\_unit|String|Month|The billing cycle of the nodes. This parameter is required if `instance_charge_type` is set to `PrePaid`.

 Valid value: `Month`. |
|platform|String|AliyunLinux|The release version of the operating system. Valid values:

 -   `CentOS`
-   `AliyunLinux`
-   `Windows`
-   `WindowsCore` |
|ram\_policy|String|KubernetesWorkerRole-021dc54f-929b-437a-8ae0-34c24d3e\*\*\*\*|The name of the worker RAM role. The RAM role is assigned to the worker nodes of the cluster to allow the worker nodes to manage ECS instances. |
|spot\_strategy|String|NoSpot|The bidding policy of preemptible instances. Valid values:

 -   NoSpot: non-preemptible instance.
-   SpotWithPriceLimit: specifies the highest bid for the preemptible instance.
-   SpotAsPriceGo: automatically submits bids based on the up-to-date market price.

 For more information, see [Preemptible instances](~~157759~~). |
|spot\_price\_limit|Array| |The bid configurations of preemptible instances. |
|instance\_type|String|ecs.c6.large|The instance type of preemptible instances. |
|price\_limit|String|0.39|The price limit of a preemptible instance. Unit: USD/hour. |
|rds\_instances|Array of String|rm-xxx|The ApsaraDB RDS instance. |
|scaling\_group\_id|String|asg-2zeieod8giqmov7z\*\*\*\*|The ID of the scaling group. |
|scaling\_policy|String|release|The scaling mode of the scaling group. Valid values:

 -   `release`: the standard mode. ECS instances are created and released based on the resource usage.
-   `recycle`: the swift mode. ECS instances are created, stopped, or started during scaling events. This reduces the time required for the next scale-out event. During the time when the instance is stopped, you are charged for only the storage service. Elastic container instances with local disks attached are not included. |
|security\_group\_id|String|sg-2ze60ockeekspl3d\*\*\*\*|The ID of the security group. |
|system\_disk\_category|String|cloud\_efficiency|The type of system disk. Valid values:

 -   `cloud_efficiency`: ultra disk.
-   `cloud_ssd`: standard SSD. |
|system\_disk\_size|Long|120|The system disk size of a worker node. Unit: GiB.

 Valid values: 20 to 500. |
|tags|Array of [tag](/intl.en-US/API Reference/Commonly used parameters.md)| |You can add labels to the ECS instances.

 A key must be unique and cannot exceed 128 characters in length. Neither keys nor values can start with aliyun or acs:. Neither keys nor values can contain https:// or http://. |
|vswitch\_ids|Array of String|vsw-2ze3ds0mdip0hdz8i\*\*\*\*|The IDs of vSwitches. |
|login\_password|String|\*\*\*\*\*\*\*\*|The SSH logon password that is used to log on to the nodes. You must set this parameter or `key_pair`. The password must be 8 to 30 characters in length, and must contain at least three of the following character types: uppercase letters, lowercase letters, digits, and special characters.

 For security purposes, the returned password is encrypted. |
|key\_pair|String|pro-nodepool|The name of the key pair that is used to log on to the nodes. You must set this parameter or `login_password`. You must set `key_pair` if the node pool is a managed node pool. |
|status|object| |The state of the node pool. |
|failed\_nodes|Long|0|The number of failed nodes. |
|healthy\_nodes|Long|3|The number of healthy nodes. |
|initial\_nodes|Long|0|The number of nodes that are being created. |
|offline\_nodes|Long|0|The number of offline nodes. |
|removing\_nodes|Long|0|The number of nodes that are being removed. |
|serving\_nodes|Long|3|The number of running nodes. |
|state|String|active|The state of the node pool. Valid values:

 -   `Active`: The node pool is active.
-   `scaling`: The node pool is being scaled.
-   `removing`: Nodes are being removed from the node pool.
-   `Deleting`: The node pool is being deleted.
-   `updating`: The node pool is being updated. |
|total\_nodes|Long|3|The total number of nodes in the node pool. |
|tee\_config|object| |The configurations of a node pool that supports confidential computing. |
|tee\_enable|Boolean|false|Indicates whether confidential computing is enabled. Valid values:

 -   `true`: Confidential computing is enabled.
-   `False`: Confidential computing is disabled. |
|management|object| |The configurations of managed node pools. |
|enable|Boolean|true|Indicates whether managed node pools are enabled. Valid values:

 -   `true`: Managed node pools are enabled.
-   `false`: Managed node pools are disabled. The other parameters in this section take effect only when `enable=true` is specified. |
|auto\_repair|Boolean|true|Indicates whether auto repairing is enabled. Auto repairing is enabled only when `enable=true` is specified.

 -   `true`: Auto repairing is enabled.
-   `false`: Auto repairing is disabled. |
|upgrade\_config|object| |The configurations of auto upgrading. The configurations take effect only when `enable=true` is specified. |
|auto\_upgrade|Boolean|true|Indicates whether auto upgrading is enabled. Valid values:

 -   `true`: Auto upgrading is enabled.
-   `false`: Auto upgrading is disabled. |
|surge|Long|5|The number of extra nodes that are added to the node pool during an auto upgrade. |
|surge\_percentage|Long|50|The percentage of extra nodes to the nodes in the node pool. You must set this parameter or `surge`. |
|max\_unavailable|Long|1|The maximum number of nodes that can be in the unschedulable state. Valid values: 1 to 1000.

 Default value: 1. |

## Examples

Sample requests

```
GET /clusters/c23421cfa74454bc8b37163fd19af****/nodepools/np31da1b38983f4511b490fc62108a**** HTTP/1.1 
Content-Type:application/json
```

Sample success responses

`XML` format

```
HTTP/1.1 200 OK
Content-Type:application/xml

<auto_scaling>
    <eip_bandwidth>5</eip_bandwidth>
    <eip_internet_charge_type>PayByBandwidth</eip_internet_charge_type>
    <enable>true</enable>
    <is_bond_eip>true</is_bond_eip>
    <max_instances>10</max_instances>
    <min_instances>1</min_instances>
    <type>cpu</type>
</auto_scaling>
<kubernetes_config>
    <cms_enabled>true</cms_enabled>
    <cpu_policy>none</cpu_policy>
    <labels>
        <key>env</key>
        <value>prod</value>
    </labels>
    <runtime>docker</runtime>
    <runtime_version>19.03.5</runtime_version>
    <taints>
        <key>key</key>
        <value>value</value>
        <effect>NoSchedule</effect>
    </taints>
    <user_data>IyEvYmluL3NoCmVjaG8gIkhlbGxvIEFDSyEi</user_data>
</kubernetes_config>
<nodepool_info>
    <created>2020-09-27T19:14:09.156823496+08:00</created>
    <is_default>true</is_default>
    <name>default-nodepool</name>
    <nodepool_id>np615c0e0966124216a0412e10afe0****</nodepool_id>
    <region_id>cn-beijing</region_id>
    <resource_group_id>rg-acfmyvw3wjmb****</resource_group_id>
    <type>ess</type>
    <updated>2020-09-27T20:37:46+08:00</updated>
</nodepool_info>
<scaling_group>
    <auto_renew>false</auto_renew>
    <auto_renew_period>1</auto_renew_period>
    <data_disks>
        <category>cloud_ssd</category>
        <size>40</size>
        <encrypted>true</encrypted>
        <auto_snapshot_policy_id>sp-2zej1nogjvovnz4z****</auto_snapshot_policy_id>
    </data_disks>
    <image_id>aliyun_2_1903_x64_20G_alibase_20200529.vhd</image_id>
    <instance_charge_type>PostPaid</instance_charge_type>
    <instance_types>ecs.n4.large</instance_types>
    <multi_az_policy>BALANCE</multi_az_policy>
    <on_demand_base_capacity>0</on_demand_base_capacity>
    <on_demand_percentage_above_base_capacity>20</on_demand_percentage_above_base_capacity>
    <spot_instance_pools>5</spot_instance_pools>
    <spot_instance_remedy>false</spot_instance_remedy>
    <compensate_with_on_demand>true</compensate_with_on_demand>
    <period>0</period>
    <period_unit>Month</period_unit>
    <platform>AliyunLinux</platform>
    <ram_policy>KubernetesWorkerRole-021dc54f-929b-437a-8ae0-34c24d3e****</ram_policy>
    <spot_strategy>NoSpot</spot_strategy>
    <spot_price_limit>
        <instance_type>ecs.c6.large</instance_type>
        <price_limit>0.39</price_limit>
    </spot_price_limit>
    <rds_instances>rm-xxx</rds_instances>
    <scaling_group_id>asg-2zeieod8giqmov7z****</scaling_group_id>
    <scaling_policy>release</scaling_policy>
    <security_group_id>sg-2ze60ockeekspl3d****</security_group_id>
    <system_disk_category>cloud_efficiency</system_disk_category>
    <system_disk_size>120</system_disk_size>
    <tags>
        <key>env</key>
        <value>prod</value>
    </tags>
    <vswitch_ids>vsw-2ze3ds0mdip0hdz8i****</vswitch_ids>
    <login_password>********</login_password>
    <key_pair>pro-nodepool</key_pair>
</scaling_group>
<status>
    <failed_nodes>0</failed_nodes>
    <healthy_nodes>3</healthy_nodes>
    <initial_nodes>0</initial_nodes>
    <offline_nodes>0</offline_nodes>
    <removing_nodes>0</removing_nodes>
    <serving_nodes>3</serving_nodes>
    <state>active</state>
    <total_nodes>3</total_nodes>
</status>
<tee_config>
    <tee_enable>false</tee_enable>
</tee_config>
<management>
    <enable>true</enable>
    <auto_repair>true</auto_repair>
    <upgrade_config>
        <auto_upgrade>true</auto_upgrade>
        <surge>5</surge>
        <surge_percentage>50</surge_percentage>
        <max_unavailable>1</max_unavailable>
    </upgrade_config>
</management>
```

`JSON` format

```
HTTP/1.1 200 OK
Content-Type:application/json

{
  "auto_scaling" : {
    "eip_bandwidth" : 5,
    "eip_internet_charge_type" : "PayByBandwidth",
    "enable" : true,
    "is_bond_eip" : true,
    "max_instances" : 10,
    "min_instances" : 1,
    "type" : "cpu"
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
    "user_data" : "IyEvYmluL3NoCmVjaG8gIkhlbGxvIEFDSyEi"
  },
  "nodepool_info" : {
    "created" : "2020-09-27T19:14:09.156823496+08:00",
    "is_default" : true,
    "name" : "default-nodepool",
    "nodepool_id" : "np615c0e0966124216a0412e10afe0****",
    "region_id" : "cn-beijing",
    "resource_group_id" : "rg-acfmyvw3wjmb****",
    "type" : "ess",
    "updated" : "2020-09-27T20:37:46+08:00"
  },
  "scaling_group" : {
    "auto_renew" : false,
    "auto_renew_period" : 1,
    "data_disks" : [ {
      "category" : "cloud_ssd",
      "size" : 40,
      "encrypted" : "true",
      "auto_snapshot_policy_id" : "sp-2zej1nogjvovnz4z****"
    } ],
    "image_id" : "aliyun_2_1903_x64_20G_alibase_20200529.vhd",
    "instance_charge_type" : "PostPaid",
    "instance_types" : [ "ecs.n4.large" ],
    "multi_az_policy" : "BALANCE",
    "on_demand_base_capacity" : 0,
    "on_demand_percentage_above_base_capacity" : 20,
    "spot_instance_pools" : 5,
    "spot_instance_remedy" : false,
    "compensate_with_on_demand" : true,
    "period" : 0,
    "period_unit" : "Month",
    "platform" : "AliyunLinux",
    "ram_policy" : "KubernetesWorkerRole-021dc54f-929b-437a-8ae0-34c24d3e****",
    "spot_strategy" : "NoSpot",
    "spot_price_limit" : [ {
      "instance_type" : "ecs.c6.large",
      "price_limit" : "0.39"
    } ],
    "rds_instances" : [ "rm-xxx" ],
    "scaling_group_id" : "asg-2zeieod8giqmov7z****",
    "scaling_policy" : "release",
    "security_group_id" : "sg-2ze60ockeekspl3d****",
    "system_disk_category" : "cloud_efficiency",
    "system_disk_size" : 120,
    "tags" : [ {
      "key" : "env",
      "value" : "prod"
    } ],
    "vswitch_ids" : [ "vsw-2ze3ds0mdip0hdz8i****" ],
    "login_password" : "********",
    "key_pair" : "pro-nodepool"
  },
  "status" : {
    "failed_nodes" : 0,
    "healthy_nodes" : 3,
    "initial_nodes" : 0,
    "offline_nodes" : 0,
    "removing_nodes" : 0,
    "serving_nodes" : 3,
    "state" : "active",
    "total_nodes" : 3
  },
  "tee_config" : {
    "tee_enable" : false
  },
  "management" : {
    "enable" : true,
    "auto_repair" : true,
    "upgrade_config" : {
      "auto_upgrade" : true,
      "surge" : 5,
      "surge_percentage" : 50,
      "max_unavailable" : 1
    }
  }
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

