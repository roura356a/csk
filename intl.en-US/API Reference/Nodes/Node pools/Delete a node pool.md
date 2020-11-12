# Delete a node pool

You can call DeleteClusterNodepool to delete a node pool.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DeleteClusterNodepool&type=ROA&version=2015-12-15)

## Request parameter types

This operation uses only common request parameters. For more information, see Common parameters.

## Request syntax

```
delete /clusters/{ClusterId}/nodepools/{NodepoolId} http|https
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|c61da77e8bfbc4c4c999af2b51b65\*\*\*\*|The ID of the cluster. |
|NodepoolId|String|Yes|np30db56bcac7843dca90b999c8928\*\*\*\*|The ID of the node pool. |

## Examples

Sample requests

```
DELETE /clusters/{ClusterId}/nodepools/{NodepoolId} HTTP/1.1
Common request parameters
{
    "ClusterId":"c61da77e8bfbc4c4c999af2b51b65****",
    "NodepoolId":"np30db56bcac7843dca90b999c8928****"
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

