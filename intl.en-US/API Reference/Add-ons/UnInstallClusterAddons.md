# UnInstallClusterAddons

Uninstalls add-ons from a cluster of Container Service for Kubernetes \(ACK\).

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=UnInstallClusterAddons&type=ROA&version=2015-12-15)

## Request headers

This operation uses only common request headers. For more information, see the Common request parameters topic.

## Request syntax

```
POST /clusters/{ClusterId}/components/uninstall HTTP|HTTPS
```

## Request parameters

|Parameter|Type|Position|Required|Example|Description|
|---------|----|--------|--------|-------|-----------|
|ClusterId|String|Path|Yes|c5b5e80b0b64a4bf6939d2d8fbbc5\*\*\*\*|The ID of the cluster. |
| |Array|Body|No| |The list of add-ons. The list is an array. |
|name|String|Body|No|ack-node-problem-detector|The name of the add-on. |

## Examples

Sample requests

```
POST /clusters/[ClusterId]/components/uninstall HTTP/1.1
Common request header
{
    "ClusterId":"c5b5e80b0b64a4bf6939d2d8fbbc5****"
}
```

Sample request description

```
POST /clusters/c3fb96524f9274b4495df0f12a6b5****/components/uninstall? RegionId=cn-hangzhou --header "Content-Type=application/json;" --body '[{"name":"ack-node-problem-detector"}]'
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

