# Upgrade a cluster

You can call UpgradeCluster to upgrade a cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=UpgradeCluster&type=ROA&version=2015-12-15)

## Request parameter types

This operation uses only common request parameters. For more information, see Common parameters.

## Request syntax

```
post /api/v2/clusters/{ClusterId}/upgrade http|https
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|c82e6987e2961451182edacd74faf\*\*\*\*|The ID of the cluster. |
|component\_name|String|No|k8s|The name of the component. |
|version|String|No|1.14.8-aliyun.1|The current version of the cluster. |
|next\_version|String|No|1.16.9-aliyun.1|The cluster version to which you want to upgrade. |

## Examples

Sample requests

```
POST /api/v2/clusters/[ClusterId]/upgrade HTTP/1.1
Common request parameters
{
    "ClusterId":"c82e6987e2961451182edacd74faf****",
    "component_name":"k8s",
    "version":"1.14.8-aliyun.1",
    "next_version":"1.16.9-aliyun.1"
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

