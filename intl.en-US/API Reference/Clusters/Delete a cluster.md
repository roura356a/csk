# Delete a cluster

You can call DeleteCluster to delete the cluster of a specified ID and release all nodes in the cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DeleteCluster&type=ROA&version=2015-12-15)

## Request parameter types

This operation uses only common request parameters. For more information, see Common parameters.

## Request syntax

```
delete /clusters/{ClusterId} http|https
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|Cccfd68c474454665ace07efce924\*\*\*\*|The ID of the cluster. |
|retain\_resources|Array of String|No|sg-2ze6ess9kho6fdn9\*\*\*\*|The name of the resource. |

## Examples

Sample requests

```
DELETE /clusters/[ClusterId] HTTP/1.1
Common request parameters
{
    "ClusterId":"Cccfd68c474454665ace07efce924****"
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

