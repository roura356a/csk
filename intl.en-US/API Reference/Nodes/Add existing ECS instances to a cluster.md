# Add existing ECS instances to a cluster

You can call AttachInstances to add existing Elastic Compute Service \(ECS\) instances to a cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=AttachInstances&type=ROA&version=2015-12-15)

## Request parameter types

This operation uses only common request parameters. For more information, see Common parameters.

## Request syntax

```
post /clusters/{ClusterId}/attach http|https
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|c106f377e16f34eb1808d6b9362c9\*\*\*\*|The ID of the cluster. |
|body|Object|No| |The request body. |
|cpu\_policy|String|No|none|The CPU management policy. |
|format\_disk|Boolean|No|false|Specifies whether to format data disks. |
|image\_id|String|No|aliyun\_2\_1903\_x64\_20G\_alibase\_20200529.vhd|The ID of the custom image. |
|instances|Array of String|No|i-2zed0sswuau6o89b\*\*\*\*|The IDs of the ECS instances. |
|is\_edge\_worker|Boolean|No|false|Specifies whether the node that you want to add is an Edge Node Service \(ENS\) instance. |
|keep\_instance\_name|Boolean|No|true|Specifies whether to retain the names of the ECS instances. |
|key\_pair|String|No|\*\*\*|The name of the key pair. You must set key\_pair or password. |
|nodepool\_id|String|No|np615c0e0966124216a0412e10afe0\*\*\*\*|The ID of the node pool. |
|password|String|No|Hello1234|The SSH logon password. You must set password or key\_pair. |
|rds\_instances|Array of String|No|rds-xxx|The IDs of the RDS instances. |
|runtime|Object|No| |The configuration of the container runtime. |
|name|String|No|docker|The name of the container runtime. |
|version|String|No|19.03.5|The version of the container runtime. |
|tags|Array|No| |The tags of the cluster. |
|key|String|No|k-aa|The key of the tag. |
|value|String|No|v-aa|The value of the tag. |
|user\_data|String|No|\*\*\*|The user data of the node. |

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|list|Array of list| |A list of the ECS instances. |
|code|String|"200"|The HTTP status code. |
|instanceId|String|"i-2ze0lgm3y6iylcbt\*\*\*\*"|The IDs of the nodes. |
|message|String|"successful"|Indicates whether the ECS instance is added to the cluster. |
|task\_id|String|"T-5a544aff80282e39ea000039"|The ID of the task. |

## Examples

Sample requests

```
POST /clusters/[ClusterId]/attach HTTP/1.1
Common request parameters
{
    "ClusterId": "c106f377e16f34eb1808d6b9362c9****",
    "instances":[
        "i-wz9e8pvnjalxkggk****"
    ],
    "format_disk":true,
    "image_id":"centos_7_7_x64_20G_alibase_20191225.vhd",
    "rds_instances":[
        "rds1",
        "rds2"
    ],
    "user_data":"ZWNobyAxMjM0NTY=",
    "tags":[
        {
            "key":"foo",
            "value":"bar"
        }
    ],
    "keep_instance_name":true,
    "cpu_policy":"none",
    "runtime":{
        "name":"docker",
        "version":"19.03.5"
    },
    "password":"Hello@1234"
}
```

Sample success responses

`XML` format

```
<task_id>T-5a544aff80282e39ea000039</task_id>
<list>
    <code>200</code>
    <instanceId>i-2ze0lgm3y6iylcbt****</instanceId>
    <message>successful</message>
</list>
```

`JSON` format

```
{
    "task_id": "T-5a544aff80282e39ea000039",
    "list": [
        {
            "code": "200",
            "instanceId": "i-2ze0lgm3y6iylcbt****",
            "message": "successful"
        }
    ]
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

