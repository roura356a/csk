# GetUpgradeStatus

Queries the upgrade status of a specified Container Service for Kubernetes \(ACK\) cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=GetUpgradeStatus&type=ROA&version=2015-12-15)

## Request syntax

```
GET /api/v2/clusters/ClusterId/upgrade/status HTTP/1.1 
Content-Type:application/json
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|c106f377e16f34eb1808d6b9362c9\*\*\*\*|The ID of the ACK cluster. |

## Response syntax

```
HTTP/1.1 200
Content-Type:application/json
{
  "error_message" : "String",
  "precheck_report_id" : "String",
  "status" : "String",
  "upgrade_step" : "String",
  "upgrade_task" : {
    "status" : "String",
    "message" : "String"
  }
}
```

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|error\_message|String|subject to actual return|The error message returned during the upgrade. |
|precheck\_report\_id|String|be4c8eb72de94d459ea7ace7c811d119|The ID of the precheck report. |
|status|String|running|The state of the upgrade. |
|upgrade\_step|String|prechecking|The current phase of the upgrade. |
|upgrade\_task|object| |The details of the upgrade task. |
|status|String|running|The state of the upgrade task. |
|message|String|subject to actual return|The description of the upgrade task. |

## Examples

Sample requests

```
GET /api/v2/clusters/c106f377e16f34eb1808d6b9362c9****/upgrade/status HTTP/1.1 
Content-Type:application/json
```

Sample success responses

`XML` format

```
HTTP/1.1 200 OK
Content-Type:application/xml

<error_message>subject to actual return</error_message>
<precheck_report_id>be4c8eb72de94d459ea7ace7c811d119</precheck_report_id>
<status>running</status>
<upgrade_step>prechecking</upgrade_step>
<upgrade_task>
    <status>running</status>
    <message>subject to actual return</message>
</upgrade_task>
```

`JSON` format

```
HTTP/1.1 200 OK
Content-Type:application/json

{
  "error_message" : "subject to actual return",
  "precheck_report_id" : "be4c8eb72de94d459ea7ace7c811d119",
  "status" : "running",
  "upgrade_step" : "prechecking",
  "upgrade_task" : {
    "status" : "running",
    "message" : "subject to actual return"
  }
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

