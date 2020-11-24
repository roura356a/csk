# CancelComponentUpgrade

Cancels an add-on upgrade in a Container Service for Kubernetes \(ACK\) cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=CancelComponentUpgrade&type=ROA&version=2015-12-15)

## Request parameter types

This operation uses only common request parameters. For more information, see Common parameters.

## Request syntax

```
POST /clusters/{clusterid}/components/{componentid}/cancel HTTP|HTTPS
```

## Request parameters

|Parameter|Type|Position|Required|Example|Description|
|---------|----|--------|--------|-------|-----------|
|clusterid|String|Path|Yes|c82e6987e2961451182edacd74faf\*\*\*\*|The ID of the ACK cluster. |
|componentid|String|Path|Yes|metric-server|The ID of the add-on. |

## Examples

Sample requests

```
POST /clusters/[clusterid]/components/[componentid]/cancel HTTP/1.1
Common request parameters
{
    "clusterid":"c82e6987e2961451182edacd74faf****",
    "componentid":"metric-server"
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

