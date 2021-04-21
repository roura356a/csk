# Query the upgrade status of a cluster

You can call GetUpgradeStatus to query the upgrade status of a cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=GetUpgradeStatus&type=ROA&version=2015-12-15)

## Request parameter types

This operation uses only common request parameters. For more information, see Common parameters.

## Request syntax

```
get /api/v2/clusters/{ClusterId}/upgrade/status http|https
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|c106f377e16f34eb1808d6b9362c9\*\*\*\*|The ID of the cluster. |

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|error\_message|String|"\*\*\*"|The error message that is returned during the upgrade. |
|precheck\_report\_id|String|"be4c8eb72de94d459ea7ace7c811d119"|The ID of the precheck report. |
|status|String|"running"|The upgrade status of the cluster. |
|upgrade\_step|String|"prechecking"|The current phase of the upgrade. |

## Examples

Sample requests

```
GET /api/v2/clusters/[ClusterId]/upgrade/status HTTP/1.1
Common request parameters
{
    "ClusterId":"c106f377e16f34eb1808d6b9362c9****"
}
```

Sample success responses

`XML` format

```
<error_message>***</error_message>
<precheck_report_id>be4c8eb72de94d459ea7ace7c811d119</precheck_report_id>
<status>running</status>
<upgrade_step>prechecking</upgrade_step>
```

`JSON` format

```
{
    "error_message": "***",
    "precheck_report_id": "be4c8eb72de94d459ea7ace7c811d119",
    "status": "running",
    "upgrade_step": "prechecking"
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

