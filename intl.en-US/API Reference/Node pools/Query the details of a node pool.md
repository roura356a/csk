# Query the details of a node pool

You can call DescribeClusterNodePoolDetail to query the details of a node pool.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DescribeClusterNodePoolDetail&type=ROA&version=2015-12-15)

## Request parameter types

This operation uses only common request parameters. For more information, see Common parameters.

## Request syntax

```
get /clusters/{ClusterId}/nodepools/{NodepoolId} http|https
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|c23421cfa74454bc8b37163fd19af\*\*\*\*|The ID of the cluster. |
|NodepoolId|String|Yes|np31da1b38983f4511b490fc62108a\*\*\*\*|The ID of the node pool. |

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|auto\_scaling|Object| |The configuration of auto scaling. |
|eip\_bandwidth|Long|"5"|The peak bandwidth of the elastic IP address \(EIP\). |
|eip\_internet\_charge\_type|String|"\*\*\*"|The billing method of the EIP. |
|enable|Boolean|"true"|Indicates whether auto scaling is enabled for the node pool. |
|health\_check\_type|String|"\*\*\*"|The health check method of the nodes for auto scaling. |
|is\_bond\_eip|Boolean|"true"|Indicates whether an EIP is bound. |
|max\_instances|Long|"10"|The maximum number of nodes. |
|min\_instances|Long|"2"|The minimum number of nodes. |
|type|String|"\*\*\*"|The type of the nodes for auto scaling. |
|kubernetes\_config|Object| |The configuration of the cluster. |
|cms\_enabled|Boolean|"true"|Indicates whether Cloud Monitor is enabled. |
|cpu\_policy|String|"none"|The CPU management policy. |
|labels|Array of labels| |The tags of the ECS instance. |
|key|String|"k-aa"|The key of the tag. |
|value|String|"v-aa"|The value of the tag. |
|node\_name\_mode|String|"nodeip"|The naming rule of the node. |
|overwrite\_hostname|Boolean|"false"|Indicates whether the host name is overwritten. |
|runtime|String|"docker"|The name of the container runtime. |
|runtime\_version|String|"19.03.5"|The version of the container runtime. |
|taints|Array of taints| |The taints of the node. |
|effect|String|"NoSchedule"|The scheduling policy. |
|key|String|"k-bb"|The key of the taint. |
|value|String|"v-bb"|The value of the taint. |
|user\_data|String|"\*\*\*"|The user data of the node. |
|nodepool\_info|Object| |The configuration of the node pool. |
|created|String|"2020-09-27T19:14:09.156823496+08:00"|The time when the node pool was created. |
|is\_default|Boolean|"true"|Indicates whether the node pool is the default node pool. |
|name|String|"default-nodepool"|The name of the node pool. |
|nodepool\_id|String|"np615c0e0966124216a0412e10afe0\*\*\*\*"|The ID of the node pool. |
|region\_id|String|"cn-beijing"|The ID of the region. |
|resource\_group\_id|String|"\*\*\*"|The ID of the resource group. |
|type|String|"ess"|The source of the node. |
|updated|String|"2020-09-27T20:37:46+08:00"|The time when the node pool was updated. |
|scaling\_group|Object| |The configuration of the scaling group. |
|auto\_renew|Boolean|"false"|Indicates whether auto renewal is enabled for nodes. Valid values:````

 -   true: Auto renewal is enabled.
-   false: Auto renewal is disabled. |
|auto\_renew\_period|Long|"0"|The auto renewal period for nodes. Valid values: 1, 2, 3, 6, and 12.`` |
|data\_disks|Array of data\_disk| |The configuration of data disks. |
|category|String|"cloud\_efficiency"|The type of data disks. Valid values:

 -   cloud\_efficiency: ultra disks.
-   cloud\_ssd: SSDs. |
|encrypted|Boolean|"false"|Indicates whether data disks are encrypted. |
|size|String|"40"|The size of a data disk. |
|image\_id|String|"aliyun\_2\_1903\_x64\_20G\_alibase\_20200529.vhd"|The ID of the custom image. |
|instance\_charge\_type|String|"PostPaid"|The billing method of the nodes. Valid values:

 -   PrePaid: subscription.
-   PostPaid: pay-as-you-go.

 Default value: PostPaid. |
|instance\_types|Array of String| |The ECS instance types of nodes. |
|multi\_az\_policy|String|"BALANCE"|The scaling policy for the multi-zone scaling group. |
|period|Long|"0"|The subscription duration of nodes. Valid values: 1, 2, 3, 6, and 12. |
|period\_unit|String|"\*\*\*"|The unit of the subscription duration. A value of Month indicates that the subscription duration is measured in months. |
|platform|String|"AliyunLinux"|The release version of the operating system. Valid values:

 -   CentOS
-   AliyunLinux
-   Windows
-   WindowsCore |
|ram\_policy|String|"KubernetesWorkerRole-021dc54f-929b-437a-8ae0-34c24d3e\*\*\*\*"|The name of the RAM role. |
|rds\_instances|Array of String| |The RDS instances. |
|scaling\_group\_id|String|"asg-2zeieod8giqmov7z\*\*\*\*"|The ID of the scaling group. |
|scaling\_policy|String|"release"|The policy for auto scaling. |
|security\_group\_id|String|"scaling\_policye7ws"|The ID of the security group. |
|system\_disk\_category|String|"cloud\_efficiency"|The type of the system disk. Valid values:

 -   cloud\_efficiency: an ultra disk.
-   cloud\_ssd: an SSD. |
|system\_disk\_size|Long|"120"|The size of the system disk. |
|tags|Array of tags| |The tags of the nodes. |
|key|String|"k-cc"|The key of the tag. |
|value|String|"v-cc"|The value of the tag. |
|vswitch\_ids|Array of String| |The IDs of VSwitches. |
|status|Object| |The status of the node pool. |
|failed\_nodes|Long|"0"|The number of failed nodes. |
|healthy\_nodes|Long|"3"|The number of healthy nodes. |
|initial\_nodes|Long|"0"|The number of nodes that are initializing. |
|offline\_nodes|Long|"0"|The number of offline nodes. |
|removing\_nodes|Long|"0"|The number of nodes that are being removed. |
|serving\_nodes|Long|"3"|The number of running nodes. |
|state|String|"active"|The status of the node pool. |
|total\_nodes|Long|"3"|The total number of nodes in the node pool. |
|tee\_config|Object| |The configuration of confidential computing. |
|tee\_enable|Boolean|"false"|Indicates whether confidential computing is enabled for the node pool. |

## Examples

Sample requests

```
GET /clusters/{ClusterId}/nodepools/{NodepoolId} HTTP/1.1
{
    "ClusterId":"c23421cfa74454bc8b37163fd19af****",
    "NodepoolId":"np31da1b38983f4511b490fc62108a****"
}
```

Sample success responses

`XML` format

```
<tee_config>
    <tee_enable>false</tee_enable>
</tee_config>
<scaling_group>
    <instance_types>ecs.n4.large</instance_types>
    <scaling_group_id>asg-2zeieod8giqmov7z****</scaling_group_id>
    <auto_renew_period>0</auto_renew_period>
    <period>0</period>
    <period_unit> </period_unit>
    <security_group_id>scaling_policye7ws</security_group_id>
    <multi_az_policy>BALANCE</multi_az_policy>
    <system_disk_size>120</system_disk_size>
    <platform>AliyunLinux</platform>
    <tags>
        <value>v-cc</value>
        <key>k-cc</key>
    </tags>
    <data_disks>
        <encrypted>false</encrypted>
        <size>40</size>
        <category>cloud_efficiency</category>
    </data_disks>
    <ram_policy>KubernetesWorkerRole-021dc54f-929b-437a-8ae0-34c24d3e****</ram_policy>
    <auto_renew>false</auto_renew>
    <instance_charge_type>PostPaid</instance_charge_type>
    <system_disk_category>cloud_efficiency</system_disk_category>
    <rds_instances>rds-xxx</rds_instances>
    <image_id>aliyun_2_1903_x64_20G_alibase_20200529.vhd</image_id>
    <scaling_policy>release</scaling_policy>
    <vswitch_ids>vsw-2ze3ds0mdip0hdz8i****</vswitch_ids>
</scaling_group>
<kubernetes_config>
    <runtime_version>19.03.5</runtime_version>
    <cpu_policy>none</cpu_policy>
    <cms_enabled>true</cms_enabled>
    <overwrite_hostname>false</overwrite_hostname>
    <runtime>docker</runtime>
    <taints>
        <effect>NoSchedule</effect>
        <value>v-bb</value>
        <key>k-bb</key>
    </taints>
    <user_data>***</user_data>
    <node_name_mode>nodeip</node_name_mode>
    <labels>
        <value>v-aa</value>
        <key>k-aa</key>
    </labels>
</kubernetes_config>
<auto_scaling>
    <health_check_type>***</health_check_type>
    <eip_internet_charge_type>***</eip_internet_charge_type>
    <enable>true</enable>
    <max_instances>10</max_instances>
    <min_instances>2</min_instances>
    <eip_bandwidth>5</eip_bandwidth>
    <type>***</type>
    <is_bond_eip>true</is_bond_eip>
</auto_scaling>
<nodepool_info>
    <resource_group_id>***</resource_group_id>
    <created>2020-09-27T19:14:09.156823496+08:00</created>
    <name>default-nodepool</name>
    <region_id>cn-beijing</region_id>
    <is_default>true</is_default>
    <type>ess</type>
    <nodepool_id>np615c0e0966124216a0412e10afe0****</nodepool_id>
    <updated>2020-09-27T20:37:46+08:00</updated>
</nodepool_info>
<status>
    <serving_nodes>3</serving_nodes>
    <total_nodes>3</total_nodes>
    <state>active</state>
    <initial_nodes>0</initial_nodes>
    <offline_nodes>0</offline_nodes>
    <removing_nodes>0</removing_nodes>
    <failed_nodes>0</failed_nodes>
    <healthy_nodes>3</healthy_nodes>
</status>
```

`JSON` format

```
{
    "tee_config": {
        "tee_enable": "false"
    },
    "scaling_group": {
        "instance_types": "ecs.n4.large",
        "scaling_group_id": "asg-2zeieod8giqmov7z****",
        "auto_renew_period": "0",
        "period": "0",
        "period_unit": " ",
        "security_group_id": "scaling_policye7ws",
        "multi_az_policy": "BALANCE",
        "system_disk_size": "120",
        "platform": "AliyunLinux",
        "tags": [
            {
                "value": "v-cc",
                "key": "k-cc"
            }
        ],
        "data_disks": [
            {
                "encrypted": "false",
                "size": "40",
                "category": "cloud_efficiency"
            }
        ],
        "ram_policy": "KubernetesWorkerRole-021dc54f-929b-437a-8ae0-34c24d3e****",
        "auto_renew": "false",
        "instance_charge_type": "PostPaid",
        "system_disk_category": "cloud_efficiency",
        "rds_instances": "rds-xxx",
        "image_id": "aliyun_2_1903_x64_20G_alibase_20200529.vhd",
        "scaling_policy": "release",
        "vswitch_ids": "vsw-2ze3ds0mdip0hdz8i****"
    },
    "kubernetes_config": {
        "runtime_version": "19.03.5",
        "cpu_policy": "none",
        "cms_enabled": "true",
        "overwrite_hostname": "false",
        "runtime": "docker",
        "taints": [
            {
                "effect": "NoSchedule",
                "value": "v-bb",
                "key": "k-bb"
            }
        ],
        "user_data": "***",
        "node_name_mode": "nodeip",
        "labels": [
            {
                "value": "v-aa",
                "key": "k-aa"
            }
        ]
    },
    "auto_scaling": {
        "health_check_type": "***",
        "eip_internet_charge_type": "***",
        "enable": "true",
        "max_instances": "10",
        "min_instances": "2",
        "eip_bandwidth": "5",
        "type": "***",
        "is_bond_eip": "true"
    },
    "nodepool_info": {
        "resource_group_id": "***",
        "created": "2020-09-27T19:14:09.156823496+08:00",
        "name": "default-nodepool",
        "region_id": "cn-beijing",
        "is_default": "true",
        "type": "ess",
        "nodepool_id": "np615c0e0966124216a0412e10afe0****",
        "updated": "2020-09-27T20:37:46+08:00"
    },
    "status": {
        "serving_nodes": "3",
        "total_nodes": "3",
        "state": "active",
        "initial_nodes": "0",
        "offline_nodes": "0",
        "removing_nodes": "0",
        "failed_nodes": "0",
        "healthy_nodes": "3"
    }
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

