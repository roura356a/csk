# UpgradeClusterAddons

Upgrades specified one or more add-ons to the required versions in a Container Service for Kubernetes \(ACK\) cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=UpgradeClusterAddons&type=ROA&version=2015-12-15)

## Request parameter types

This operation uses only common request parameters. For more information, see Common parameters.

## Request syntax

```
POST /clusters/{ClusterId}/components/upgrade HTTP|HTTPS
```

## Request parameters

|Parameter|Type|Position|Required|Example|Description|
|---------|----|--------|--------|-------|-----------|
|ClusterId|String|Path|Yes|ack-arena|The ID of the ACK cluster. |
| |Array|Body|No| |The request body. |
|component\_name|String|Body|No|ar|The name of the add-on to upgrade. |
|next\_version|String|Body|No|0.5.0|The required version. |
|version|String|Body|No|0.5.0|The current version of the add-on. |

## Examples

Sample requests

```
POST /clusters/{ClusterId}/components/upgrade HTTP/1.1
Common request parameters
{
    "ClusterId":"cdf4994652bee42789be1d9964fa0****",
    "component_name":"ack-arena",
    "next_version":"0.5.0",
    "version":"0.5.0"
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

