# Delete a trigger

You can call DeleteKubernetesTrigger to delete an application operation trigger.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DeleteKubernetesTrigger&type=ROA&version=2015-12-15)

## Request parameter types

This operation uses only common request parameters. For more information, see Common parameters.

## Request syntax

```
delete /triggers/revoke/{Id} http|https
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|Id|String|No|111|The ID of the trigger. |

## Examples

Sample requests

```
POST /triggers HTTP/1.1
Common request parameters
{
    "Id":"c5cdf7e3938bc4f8eb0e44b21a80f****"
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

