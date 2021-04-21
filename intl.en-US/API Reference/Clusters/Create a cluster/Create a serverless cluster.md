# Create a serverless cluster

You can call CreateCluster to create a serverless cluster.

## Request information

Request line

```
POST /clusters HTTP/1.1
```

Operation-specific request parameters

No operation-specific request parameters are used. For more information, see [Common parameters](/intl.en-US/API Reference/Common parameters.md).

Request body

```
{
"cluster_type": "Ask", 
"name":"The name of the cluster.",
"region_id":"The ID of the region.",
"zoneid":"The ID of the zone.",
"nat_gateway":"true", //Specifies whether to create a Network Address Translation (NAT) gateway.
"private_zone":"true", //Specifies whether to enable PrivateZone for service discovery.
"vpc_id":"The ID of the virtual private cloud (VPC).", //If you leave this parameter empty, the system automatically creates a VPC.
"tags":"The tags of the cluster. The value must be an array.",
"vswitch_id":"The ID of the VSwitch.",
}
```

|Parameter|Type|Required|Description|
|---------|----|--------|-----------|
|cluster\_type|string|Yes|The type of the cluster.|
|name|string|Yes|The name of the cluster. The name can contain uppercase letters, lowercase letters, digits, and hyphens \(-\).|
|kubernetes\_version|string|No|The version of the cluster.|
|private\_zone|bool|Yes|Specifies whether to enable PrivateZone for service discovery. Valid values: true and false. For more information, see [Use the service discovery feature based on Alibaba Cloud DNS PrivateZone in ASK clusters](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Serverless cluster best practices/Use the service discovery feature based on Alibaba Cloud DNS PrivateZone in ASK clusters.md).|
|region\_id|string|Yes|The ID of the region where the cluster is deployed.|
|endpoint\_public\_access|bool|No|Specifies whether to enable Internet access to the endpoint of the cluster.|
|zone\_id|string|Yes|The ID of the zone to which the cluster belongs.|
|vswitch\_id|string|No|Optional. The ID of the VSwitch. If you do not set this parameter, the system automatically creates a VSwitch that uses the CIDR block 192.168.0.0/16.|
|tags|list|No|The tags of the cluster. -   key: the key of the tag.
-   value: the value of the tag. |
|deletion\_protection|bool|No|Specifies whether to enable deletion protection for the cluster.|
|addons|Array|No|The add-ons to be installed for the cluster. A serverless cluster only supports Log Service.-   Configure the following parameters for add-ons:
    -   name: the name of the add-on. This parameter is required.
    -   config: This parameter is optional.
    -   disabled: specifies whether to disable default installation. This parameter is optional.
-   Log Service: This parameter is optional. If Log Service is disabled, the cluster auditing feature is unavailable. You can use the following types of Log Service projects:
    -   Create a project``: \[\{"name":"logtail-ds","config":""\}\]
    -   Use an existing project``: \[\{"name":"logtail-ds","config":"\{"sls\_project\_name":"your\_sls\_project\_name"\}"\}\] |
|nat\_gateway|bool|No|Specifies whether to create a NAT gateway. Valid values: true and false. Default value: false.|
|vpc\_id|string|No|The ID of the VPC. You can leave this parameter empty. If you do not set this parameter, the system automatically creates a VPC that uses the CIDR block 192.168.0.0/16. **Note:** You must specify both the vpc\_id and vswitch\_ids parameters or leave both parameters empty. |
|vswitch\_ids|list|No|The IDs of VSwitches. If you leave this parameter empty, the system automatically creates a VSwitch.**Note:** You must specify both the vpc\_id and vswitch\_ids parameters or leave both parameters empty. |
|security\_group\_id|string|No|The ID of the security group. If you leave this parameter empty, the system automatically creates a security group.|

## Response information

Response line

```
HTTP/1.1 202 Accepted
```

Operation-specific response parameters

No operation-specific response parameters are used. For more information, see [Common parameters](/intl.en-US/API Reference/Common parameters.md).

|Parameter|Type|Description|
|---------|----|-----------|
|cluster\_id|string|The ID of the cluster.|
|request\_id|string|The ID of the request.|
|task\_id|string|The ID of the task. The task ID is automatically assigned by the system. You can use the task ID to query the task status.|

## Examples

Sample requests

```
POST /clusters HTTP/1.1
<Common request parameters>
{
    "cluster_type":"Ask",
    "name":"ask-cluster",
    "kubernetes_version":"1.16.9-aliyun.1",
    "region_id":"cn-shenzhen",
    "endpoint_public_access":true,
    "private_zone":true,
    "tags":[
        {
            "key":"tier",
            "value":"frontend"
        }
    ],
    "deletion_protection":true,
    "addons":[
        {
            "name":"logtail-ds"
        }
    ],
    "zone_id":"cn-shenzhen-a",
    "vpc_id":"vpc-wz984yvbd6lck22z3****",
    "vswitch_ids":[
        "vsw-wz9uwxhawmtzg7u9h****"
    ],
    "logging_type":"SLS",
    "security_group_id":"sg-wz9b86l4s7nthi1k****"
}
```

If you create a serverless cluster that uses an existing VPC, specify the parameters based on the following sample request:

```
POST /clusters HTTP/1.1
<Common request parameters>
{
    "cluster_type":"Ask",
    "name":"test-ask",
    "kubernetes_version":"1.16.9-aliyun.1",
    "region_id":"cn-hangzhou",
    "endpoint_public_access":false,
    "private_zone":false,
    "nat_gateway":false,
    "tags":[
        {
            "key":"k-aa",
            "value":"v-aa"
        }
    ],
    "deletion_protection":false,
    "addons":[
        {
            "name":"logtail-ds"
        }
    ],
    "zone_id":"cn-hangzhou-i"
}
```

Sample success responses

```
HTTP/1.1 202 Accepted
<Common response parameters>
{
    "cluster_id": "cb95aa626a47740afbf6aa099b650****",
    "RequestId": "687C5BAA-D103-4993-884B-C35E4314A1E1",
    "task_id": "T-5a54309c80282e39ea00002f",
}
```

