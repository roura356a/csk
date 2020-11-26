# Install add-ons for a cluster

You can call InstallClusterAddons to install add-ons for a cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=InstallClusterAddons&type=ROA&version=2015-12-15)

## Request parameter types

This operation uses only common request parameters. For more information, see Common parameters.

## Request syntax

```
post /clusters/{ClusterId}/components/install http|https
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|c82e6987e2961451182edacd74faf\*\*\*\*|The ID of the cluster. |
|body|Array|No|ags-metrics-collector|A list of add-ons. |
|config|String|No|\*\*\*|The configurations of the add-on. |
|disabled|Boolean|No|true|Specifies whether to disable automatic installation of the add-on. |
|name|String|No|ags-metrics-collector|The name of the add-on. |
|required|String|No|false|Specifies whether to enable automatic installation of the add-on. |
|version|String|No|v1.0.0.2-cc3b2d6-aliyun|The version of the add-on. |

## Examples

Sample requests

```
POST /clusters/[ClusterId]/components/install HTTP/1.1
Common request parameters
{
    "ClusterId":"c82e6987e2961451182edacd74faf****"
}
```

Sample request description

```
POST /clusters/c3fb96524f9274b4495df0f12a6b5****/components/install? RegionId=cn-hangzhou --header "Content-Type=application/json;" --body '[{"name":"ack-node-problem-detector"}]'
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

