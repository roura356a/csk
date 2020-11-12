# Query the upgrade statuses of cluster add-ons

You can call DescribeClusterAddonsUpgradeStatus to query the upgrade statuses of cluster add-ons.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DescribeClusterAddonsUpgradeStatus&type=ROA&version=2015-12-15)

## Request parameter types

This operation uses only common request parameters. For more information, see Common parameters.

## Request syntax

```
get /clusters/[ClusterId]/components/upgradestatus http|https
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|c23421cfa74454bc8b37163fd19af\*\*\*\*|The ID of the cluster. |
|componentIds|Array of String|No|metric-server|The names of add-ons. |

## Examples

Sample requests

```
GET /clusters/[ClusterId]/components/upgradestatus HTTP/1.1
Common request parameters
{
    "ClusterId":"c23421cfa74454bc8b37163fd19af****"
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

