# Cancel the upgrade of a cluster

You can call CancelClusterUpgrade to cancel the upgrade of a cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=CancelClusterUpgrade&type=ROA&version=2015-12-15)

## Request parameter types

This operation uses only common request parameters. For more information, see Common parameters.

## Request syntax

```
post /api/v2/clusters/{ClusterId}/upgrade/cancel http|https
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|c106f377e16f34eb1808d6b9362c9\*\*\*\*|The ID of the cluster. |

## Examples

Sample requests

```
POST /api/v2/clusters/[ClusterId]/upgrade/cancel HTTP/1.1
Common request parameters
{
    "ClusterId":"c106f377e16f34eb1808d6b9362c9****"
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

