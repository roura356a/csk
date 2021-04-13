# Delete an application deployment template

You can call DeleteTemplate to delete an application deployment template.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DeleteTemplate&type=ROA&version=2015-12-15)

## Request headers

This operation uses the common request header only. For more information, see Common parameters.

## Request syntax

```
DELETE /templates/[TemplateId] HTTPS|HTTP
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|TemplateId|String|Yes|20663627-81ee-4cfe-9b69-c96fcb84a674|The ID of the template. |

## Examples

Sample requests

```
DELETE /templates/[TemplateId] HTTP/1.1
Common request header
{
"TemplateId": "20663627-81ee-4cfe-9b69-c96fcb84a674"
}
```

Sample success responses

`JSON` format

```
{}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

