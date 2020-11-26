# Delete an application deployment template

You can call DeleteTemplate to delete an application deployment template.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DeleteTemplate&type=ROA&version=2015-12-15)

## Request parameter types

This operation uses common request parameters only. For more information, see Common parameters.

## Request syntax

```
DELETE /templates/[TemplateId] HTTPS|HTTP
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|TemplateId|String|Yes|20663627-81ee-4cfe-9b69-c96fcb84a674|The ID of the template. |

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|requestId|String|619f0847-8462-462f-8358-07e90e2964a1|The ID of the request. |

## Examples

Sample requests

```
DELETE /templates/[TemplateId] HTTP/1.1
Common request parameters
{
"TemplateId": "20663627-81ee-4cfe-9b69-c96fcb84a674"
}
```

Sample success responses

`XML` format

```
<requestId>619f0847-8462-462f-8358-07e90e2964a1</requestId>
```

`JSON` format

```
{"requestId":"619f0847-8462-462f-8358-07e90e2964a1"}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

