# Delete an application trigger

You can call the DeleteTrigger operation to delete an application trigger.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DeleteTrigger&type=ROA&version=2015-12-15)

## Request syntax

```
DELETE /clusters/[cluster_id]/triggers/[Id] HTTP/1.1
Content-Type:application/json
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|cluster\_id|String|No|c68deb844dc6d480eb775be611fdf\*\*\*\*|The ID of the cluster. |
|Id|String|Yes|123407|The ID of the trigger that you want to delete. |

## Response syntax

```
HTTP/1.1 200 OK
```

## Response parameters

None

## Examples

Sample requests

```
DELETE /clusters/[cluster_id]/triggers/[Id] HTTP/1.1
Host:cs.aliyuncs.com
Content-Type:application/json
```

Sample success responses

`JSON` format

```
HTTP/1.1 200 OK
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

