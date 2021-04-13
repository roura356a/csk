# Suspend the upgrade of a cluster

You can call PauseClusterUpgrade to suspend the upgrade of a cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=PauseClusterUpgrade&type=ROA&version=2015-12-15)

## Request headers

This operation uses the common request header only. For more information, see Common parameters.

## Request syntax

```
POST /api/v2/clusters/[ClusterId]/upgrade/pause HTTPS|HTTP
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|c82e6987e2961451182edacd74faf\*\*\*\*|The ID of the cluster. |

## Examples

Sample requests

```
POST /api/v2/clusters/[ClusterId]/upgrade/pause HTTP/1.1
Common request header
{
"ClusterId": " c82e6987e2961451182edacd74faf****"
}
```

Sample success responses

`JSON` format

```
{}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

