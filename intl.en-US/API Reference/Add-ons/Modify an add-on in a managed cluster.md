# Modify an add-on in a managed cluster

You can call ModifyClusterConfiguration to modify an add-on in a managed cluster and create a ConfigMap for the managed cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=ModifyClusterConfiguration&type=ROA&version=2015-12-15)

## Request parameter types

This operation uses only common request parameters. For more information, see Common parameters.

## Request syntax

```
put /clusters/{ClusterId}/configuration http|https
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|cc0f87de0b8fb403f86e10e204f83\*\*\*\*|The ID of the cluster. |
|customize\_config|Object|No| |The custom configurations of the add-on. |
|configs|Object|No| |The configurations of the add-on. |
|key|String|No|k-aa|The key of the tag. |
|value|String|No|v-aa|The value of the tag. |
|name|String|No|metric-server|The name of the add-on. |

## Examples

Sample requests

```
PUT /clusters/[ClusterId]/configuration HTTP/1.1
Common request parameters
{
    "ClusterId":"cc0f87de0b8fb403f86e10e204f83****"
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

