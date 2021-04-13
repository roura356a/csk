# Create a trigger

You can call CreateKubernetesTrigger to create an application operation trigger.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=CreateKubernetesTrigger&type=ROA&version=2015-12-15)

## Request parameter types

This operation uses only common request parameters. For more information, see Common parameters.

## Request syntax

```
post /triggers http|https
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|RegionId|String|No|cn-beijing|The ID of the region. |
|ClusterId|String|No|c5cdf7e3938bc4f8eb0e44b21a80f\*\*\*\*|The ID of the cluster. |
|ProjectId|String|No|default/web-server|The ID of the project. The value consists of an application name and the namespace where the application resides. For example: default/web-server. |
|Action|String|No|redeploy|The operation to trigger. For example, redeploy. |
|Type|String|No|deployment|The type of the application. Valid values:

 -   deployment: Create an application on the Deployments tab of the ACK console.
-   application: Create an application in Application Center. |

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|action|String|"redeploy"|The operation triggered. For example, redeploy. |
|cluster\_id|String|"c5cdf7e3938bc4f8eb0e44b21a80f\*\*\*\*"|The ID of the cluster. |
|id|String|"111"|The ID of the trigger. |
|project\_id|String|"default/web-server"|The ID of the project. |

## Examples

Sample requests

```
POST /triggers HTTP/1.1
Common request parameters
{
    "ClusterId":"c5cdf7e3938bc4f8eb0e44b21a80f****",
    "ProjectId":"default/web-server",
    "Action":"redeploy",
    "Type":"deployment"
}
```

Sample success responses

`XML` format

```
<cluster_id>c5cdf7e3938bc4f8eb0e44b21a80f****</cluster_id>
<project_id>default/web-server</project_id>
<action>redeploy</action>
<id>111</id>
```

`JSON` format

```
{
    "cluster_id": "c5cdf7e3938bc4f8eb0e44b21a80f****",
    "project_id": "default/web-server",
    "action": "redeploy",
    "id": "111"
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

