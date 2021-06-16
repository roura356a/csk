# Update node pool information

You can call ModifyClusterNodePool to update node pool information.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=ModifyClusterNodePool&type=ROA&version=2015-12-15)

## Request parameter types

This operation uses only common request parameters. For more information, see Common parameters.

## Request syntax

```
put /clusters/{ClusterId}/nodepools/{NodepoolId} http|https
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|c23421cfa74454bc8b37163fd19af\*\*\*\*|The ID of the cluster. |
|NodepoolId|String|Yes|p31da1b38983f4511b490fc62108a\*\*\*\*|The ID of the node pool. |
|body|Object|No| |The request body. |
|auto\_scaling|Object|No| |The configuration of auto scaling. |
|eip\_bandwidth|Long|No|5|The peak bandwidth of the elastic IP address \(EIP\). |
|eip\_internet\_charge\_type|String|No|\*\*\*|The billing method of the EIP. |
|enable|Boolean|No|true|Specifies whether to enable auto scaling. |
|is\_bond\_eip|Boolean|No|true|Specifies whether to bind an EIP. |
|max\_instances|Long|No|10|The maximum number of the Elastic Compute Service \(ECS\) instances. |
|min\_instances|Long|No|2|The minimum number of ECS instances. |
|type|String|No|cpu|The type of auto scaling. |
|kubernetes\_config|Object|No| |The configuration of the cluster. |
|cms\_enabled|Boolean|No|true|Specifies whether to enable Cloud Monitor. |
|image\_id|String|No|\*\*\*|The ID of the custom image. |
|labels|Array|No| |The tags of the ECS instance. |
|key|String|No|k-aa|The key of the tag. |
|value|String|No|v-aa|The value of the tag. |
|runtime|String|No|docker|The name of the container runtime. |
|runtime\_version|String|No|19.03.5|The version of the container runtime. |
|taints|Array|No| |The taints of the node. |
|effect|String|No|NoSchedule|The scheduling policy. |
|key|String|No|k-bb|The key of the taint. |
|value|String|No|v-bb|The value of the taint. |
|user\_data|String|No|\*\*\*|The user data of the node. |
|nodepool\_info|Object|No| |The configuration of the node pool. |
|name|String|No|default-nodepool|The name of the node pool. |
|resource\_group\_id|String|No|rg-acfmyvw3wjm\*\*\*\*|The ID of the resource group. |
|scaling\_group|Object|No| |The configuration of the scaling group. |
|data\_disks|Array|No| |The configuration of data disks. |
|category|String|No|cloud\_ssd|The type of data disks. |
|encrypted|String|No|false|Specifies whether to encrypt data disks. You must enable Key Management Service \(KMS\). |
|size|Long|No|40|The size of a data disk. |
|instance\_charge\_type|String|No|PostPaid|The billing method of the nodes in the node pool. Valid values:

 -   PrePaid: subscription.
-   PostPaid: pay-as-you-go. |
|instance\_types|Array of String|No|ecs.c6.large|The ECS instance type of the nodes. |
|key\_pair|String|No|\*\*\*|The name of the key pair. You must set key\_pair or login\_password. |
|login\_password|String|No|Hello1234|The SSH logon password. The password must be 8 to 30 characters in length and contain a minimum of three of the following character types: uppercase letters, lowercase letters, digits, and special characters. You must set login\_password or key\_pair. |
|rds\_instances|Array of String|No|rds-xxx|1 |
|scaling\_policy|String|No|release|The policy for auto scaling. |
|system\_disk\_category|String|No|cloud\_efficiency|The type of the system disk. |
|system\_disk\_size|Long|No|120|The size of the system disk. |
|tags|Array|No| |The tags of the nodes. |
|key|String|No|k-cc|The key of the tag. |
|value|String|No|v-cc|The value of the tag. |
|vpc\_id|String|No|vpc-wz984yvbd6lck22z3\*\*\*\*|The ID of the VPC. |
|vswitch\_ids|Array of String|No|vsw-wz9uwxhawmtzg7u9h\*\*\*\*|1 |
|tee\_config|Object|No| |The configuration of confidential computing. |
|tee\_enable|Boolean|No|false|Specifies whether to enable confidential computing for the node pool. |
|update\_nodes|Boolean|No|true|Specifies whether to update node information, such as tags and taints. |

## Examples

Sample requests

```
PUT /clusters/{ClusterId}/nodepools/{NodepoolId} HTTP/1.1
{
    "ClusterId":"c23421cfa74454bc8b37163fd19af****",
    "NodepoolId":"np31da1b38983f4511b490fc62108a****",
    "nodepool_info":{
        "name":"default-nodepool"
    },
    "scaling_group":{
        "vswitch_ids":[
            "vsw-2vc41xuumx5z2rdma****"
        ],
        "system_disk_category":"cloud_efficiency",
        "system_disk_size":40,
        "data_disks":[
            {
                "category":"cloud_essd",
                "size":40,
                "encrypted":"false"
            }
        ],
        "instance_types":[
            "ecs.c5.large"
        ],
        "vpc_id":"vpc-2vcg932hsxsxuqbgl****",
        "tags":[
            {
                "key":"ros-aliyun-created",
                "value":"k8s_nodes_config_stack_74ea2c7c-167b-4c06-acd3-a9962172****"
            }
        ],
        "instance_charge_type":"PostPaid",
        "login_password":"Hello1234",
        "rds_instances":[

        ]
    },
    "kubernetes_config":{
        "cms_enabled":true,
        "labels":[
            {
                "key":"k-aa",
                "value":"v-aa"
            }
        ],
        "taints":[
            {
                "key":"key",
                "value":"value",
                "effect":"NoSchedule"
            }
        ],
        "user_data":"",
        "runtime":"docker",
        "runtime_version":"19.03.5"
    },
    "auto_scaling":{
        "enable":false
    },
    "tee_config":{
        "tee_enable":false
    },
    "update_nodes":true
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

