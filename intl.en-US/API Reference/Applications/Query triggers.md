# Query triggers

You can call GetKubernetesTrigger to query application operation triggers.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=GetKubernetesTrigger&type=ROA&version=2015-12-15)

## Request parameter types

This operation uses only common request parameters. For more information, see Common parameters.

## Request syntax

```
get /triggers/{ClusterId} http|https
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|c5cdf7e3938bc4f8eb0e44b21a80f\*\*\*\*|The ID of the cluster. |
|Namespace|String|Yes|default|The name of the namespace. |
|Type|String|No|deployment|The type of the application. |
|Name|String|Yes|web-server|The name of the application. |

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|triggers|Array of triggers| |A list of triggers. |
|action|String|"redeploy"|The operation triggered. |
|cluster\_id|String|"c5cdf7e3938bc4f8eb0e44b21a80f\*\*\*\*"|The ID of the cluster. |
|id|String|"111"|The ID of the trigger. |
|project\_id|String|"default/web-server"|The ID of the project. |
|token|String|"\*\*\*"|The token used to enable a trigger. |

## Examples

Sample requests

```
POST /triggers/[ClusterId] HTTP/1.1
Common request parameters
{
    "ClusterId":"c5cdf7e3938bc4f8eb0e44b21a80f****",
    "Namespace":"default",
    "Name":"web-server",
    "Type":"deployment"
}
```

Sample success responses

`XML` format

```
<triggers>
      <cluster_id>c5cdf7e3938bc4f8eb0e44b21a80f****</cluster_id>
      <project_id>default/web-server</project_id>
      <action>redeploy</action>
      <id>111</id>
      <token>***</token>
</triggers>
```

`JSON` format

```
{
    "triggers": [
        {
            "cluster_id": "c5cdf7e3938bc4f8eb0e44b21a80f****",
            "project_id": "default/web-server",
            "action": "redeploy",
            "id": "111",
            "token": "***"
        }
    ]
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

