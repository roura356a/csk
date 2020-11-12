# Create a node pool

You can call CreateClusterNodePool to create a node pool for a cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=CreateClusterNodePool&type=ROA&version=2015-12-15)

## Request parameter types

This operation uses only common request parameters. For more information, see Common parameters.

## Request syntax

```
post /clusters/{ClusterId}/nodepools http|https
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|c61da77e8bfbc4c4c999af2b51b65\*\*\*\*|The ID of the cluster. |
|body|Object|No| |The request body. |
|auto\_scaling|Object|No| |The configuration of auto scaling. |
|enable|Boolean|No|true|Specifies whether to enable auto scaling. |
|max\_instances|Long|No|10|The maximum number of the Elastic Compute Service \(ECS\) instances. |
|min\_instances|Long|No|1|The minimum number of the ECS instances. |
|type|String|No|cpu|The policy for auto scaling. |
|kubernetes\_config|Object|No| |The configuration of the cluster. |
|cms\_enabled|Boolean|No|true|Specifies whether to enable Cloud Monitor. |
|cpu\_policy|String|No|none|The CPU management policy. Valid values: static and none. |
|labels|Array|No| |The tags of the ECS instances. |
|key|String|No|disk\_type|The key of the tag. |
|value|String|No|ssd|The value of the tag. |
|runtime|String|No|docker|The name of the container runtime. |
|runtime\_version|String|No|19.03.5|The version of the container runtime. |
|taints|Array|No| |The taints of the nodes. |
|effect|String|No|NoSchedule|The scheduling policy. |
|key|String|No|k-1|The key of the taint. |
|value|String|No|v-1|The value of the taint. |
|user\_data|String|No|dGhpcyBpcyBhIGV4YW1wbGU=|The user data. |
|nodepool\_info|Object|No| |The configuration of the node pool. |
|name|String|No|cluster-demo|The name of the node pool. |
|resource\_group\_id|String|No|rg-acfmyvw3wjmb\*\*\*\*|The ID of the resource group where the node pool resides. |
|scaling\_group|Object|No| |The scaling group of the node pool. |
|auto\_renew|Boolean|No|true|Specifies whether to enable auto renewal for nodes in the node pool. This parameter takes effect and is required only if `instance_charge_type` is set to `PrePaid`. Valid values:

 -   true: enables auto renewal.
-   false: disables auto renewal. |
|auto\_renew\_period|Long|No|1|The auto renewal period for nodes in the node pool. This parameter takes effect and is required only if instance\_charge\_type is set to PrePaid and auto\_renew is set to true. If `period_unit` is set to Month, valid values of auto\_renew\_period include 1, 2, 3, 6, and 12. |
|data\_disks|Array|No| |The configuration of data disks. |
|category|String|No|cloud\_ssd|The type of data disks. Valid values:

 -   cloud\_efficiency: ultra disks.
-   cloud\_ssd: SSDs. |
|encrypted|String|No|false|Specifies whether to encrypt data disks. |
|size|Long|No|40|The size of a data disk. Unit: GiB. |
|image\_id|String|No|aliyun\_2\_1903\_x64\_20G\_alibase\_20200529.vhd|The ID of the custom image. If you do not specify this parameter, a system image is used. |
|instance\_charge\_type|String|No|PrePaid|The billing method of the nodes in the node pool. Valid values:

 -   PrePaid: subscription.
-   PostPaid: pay-as-you-go.

 Default value: PostPaid. |
|instance\_types|Array of String|No|ecs.d1ne.2xlarge|The ECS instance type of the nodes. |
|key\_pair|String|No|\*\*\*|The name of the key pair. You must set key\_pair or login\_password. |
|login\_password|String|No|\*\*\*|The SSH logon password. You must set login\_password or key\_pair. |
|period|Long|No|1|The subscription duration of the nodes in the node pool. This parameter takes effect and is required only if instance\_charge\_type is set to`PrePaid`. If `period_unit` is set to Month, valid values of `period` include 1, 2, 3, 6, and 12. |
|period\_unit|String|No|Month|The unit of the subscription duration of the nodes in the node pool. This parameter is required if you set instance\_charge\_type to `PrePaid`. A value of Month specifies that the subscription duration is measured in months. |
|platform|String|No|Linux|The release version of the operating system. Valid values:

 -   CentOS
-   AliyunLinux
-   Windows
-   WindowsCore |
|rds\_instances|Array of String|No|rds-\*\*\*|1 |
|scaling\_policy|String|No|release|The policy for auto scaling. |
|security\_group\_id|String|No|sg-wz9a8g2mt6x5llu0\*\*\*\*|The ID of the security group. |
|system\_disk\_category|String|No|cloud\_efficiency|The type of the system disk. Valid values:

 -   cloud\_efficiency: an ultra disk.
-   cloud\_ssd: an SSD. |
|system\_disk\_size|Long|No|120|The size of the system disk. |
|tags|Array|No| |The tags of the nodes. |
|key|String|No|node-k-1|The key of the tag. |
|value|String|No|node-v-1|The value of the tag. |
|vpc\_id|String|No|vpc-wz984yvbd6lck22z3\*\*\*\*|The ID of the VPC. |
|vswitch\_ids|Array of String|No|vsw-wz9mfnhmssud6eicu\*\*\*\*|1 |
|tee\_config|Object|No| |The configuration of confidential computing. |
|tee\_enable|Boolean|No|true|Specifies whether to enable confidential computing for the node pool. |

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|nodepool\_id|String|"np31da1b38983f4511b490fc62108a\*\*\*\*"|The ID of the node pool. |

## Examples

Sample requests

```
POST /clusters/{ClusterId}/nodepools HTTP/1.1
Common request parameters
{
    "ClusterId":"c61da77e8bfbc4c4c999af2b51b65****",
    "nodepool_info":{
        "name":"cluster-demo",
        "resource_group_id":"rg-aek2aa7u22q****"
    },
    "scaling_group":{
        "vswitch_ids":[
            "vsw-wz9mfnhmssud6eicu****"
        ],
        "system_disk_category":"cloud_efficiency",
        "system_disk_size":120,
        "data_disks":[
            {
                "category":"cloud_ssd",
                "size":40,
                "encrypted":"false"
            }
        ],
        "instance_types":[
            "ecs.d1ne.2xlarge"
        ],
        "vpc_id":"vpc-wz984yvbd6lck22z3****",
        "tags":[
            {
                "key":"k-1",
                "value":"v-1"
            }
        ],
        "instance_charge_type":"PrePaid",
        "login_password":"****",
        "period":1,
        "period_unit":"Month",
        "auto_renew":true,
        "auto_renew_period":1,
        "security_group_id":"sg-wz9a8g2mt6x5llu0****",
        "platform":"AliyunLinux",
        "image_id":"aliyun_2_1903_x64_20G_alibase_20200529.vhd",
        "rds_instances":[

        ]
    },
    "kubernetes_config":{
        "cpu_policy":"none",
        "cms_enabled":true,
        "labels":[
            {
                "key":"k-2",
                "value":"v-2"
            }
        ],
        "taints":[
            {
                "key":"k-3",
                "value":"v-3",
                "effect":"NoSchedule"
            }
        ],
        "user_data":"MTEx",
        "runtime":"docker",
        "runtime_version":"19.03.5"
    },
    "tee_config":{
        "tee_enable":true
    }
}
```

Sample request description

```
Create a node pool that supports auto scaling:
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
<nodepool_id>np31da1b38983f4511b490fc62108a****</nodepool_id>
```

`JSON` format

```
{
    "nodepool_id": "np31da1b38983f4511b490fc62108a****"
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

