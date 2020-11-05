# Uninstall an add-on from a cluster

You can call UnInstallClusterAddons to uninstall an add-on from a cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=UnInstallClusterAddons&type=ROA&version=2015-12-15)

## Request parameter types

This operation uses common request parameters only. For more information, see Common parameters.

## Request syntax

```
POST /clusters/[ClusterId]/components/uninstall HTTPS|HTTP
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|c82e6987e2961451182edacd74faf\*\*\*\*|The ID of the cluster. |
|name|String|Yes|test-k8s|The name of the cluster. |

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|RequestId|String|2ffb6f7c-237f-491c-90fc-bb6599f23d7f|The ID of the request. |

## Examples

Sample requests

```
POST /clusters/[ClusterId]/components/uninstall HTTP/1.1
Common request parameters
{
"ClusterId": "c82e6987e2961451182edacd74faf****",
"name": "test-k8s"
}
```

Sample success responses

`XML` format

```
<RequestId>2ffb6f7c-237f-491c-90fc-bb6599f23d7f</RequestId>
```

`JSON` format

```
{"RequestId":"2ffb6f7c-237f-491c-90fc-bb6599f23d7f"}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

