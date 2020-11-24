# ScaleClusterNodePool

Scales out a specified node pool in a Container Service for Kubernetes \(ACK\) cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=ScaleClusterNodePool&type=ROA&version=2015-12-15)

## Request parameter types

This operation uses only common request parameters. For more information, see Common parameters.

## Request syntax

```
POST /clusters/{ClusterId}/nodepools/{NodepoolId} HTTP|HTTPS
```

## Request parameters

|Parameter|Type|Position|Required|Example|Description|
|---------|----|--------|--------|-------|-----------|
|ClusterId|String|Path|Yes|c23421cfa74454bc8b37163fd19af\*\*\*\*|The ID of the ACK cluster. |
|NodepoolId|String|Path|Yes|np31da1b38983f4511b490fc62108a\*\*\*\*|The ID of the node pool. |
| |Object|Body|No| |The request body. |
|count|Long|Body|No|1|The maximum number of nodes supported by the node pool after scale-out. |

## Examples

Sample requests

```
POST /clusters/{ClusterId}/nodepools/{NodepoolId} HTTP/1.1
Common request parameters
{
    "ClusterId":"c23421cfa74454bc8b37163fd19af****",
    "NodepoolId":"np31da1b38983f4511b490fc62108a****",
    "count":1
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

