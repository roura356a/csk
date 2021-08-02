# CreateTemplate

Creates an application deployment template.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=CreateTemplate&type=ROA&version=2015-12-15)

## Request syntax

```
POST /templates HTTP/1.1 
Content-Type:application/json
{
  "name" : "String",
  "template" : "String",
  "tags" : "String",
  "description" : "String",
  "template_type" : "String"
}
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|name|String|Yes|service-account-template|The name of the template.

 The name must be 1 to 63 characters in length, and can contain digits, letters, and hyphens \(-\). It cannot start with a hyphen \(-\). |
|template|String|Yes|apiVersion: v1\\nkind: ServiceAccount\\nmetadata:\\n name: test-sa|The template content in YAML format. |
|tags|String|No|test|The tag of the template. |
|description|String|No|this is test|The description of the template. |
|template\_type|String|No|kubernetes|The type of the template. You can set the parameter to a custom value.

 -   If you set the parameter to `kubernetes`, the template is displayed on the Templates page in the Container Service for Kubernetes \(ACK\) console.
-   If you set the parameter to `compose`, the template is not displayed in the ACK console.

 We recommend that you set the parameter to `kubernetes`.

 Default value: `compose`. |

## Response syntax

```
HTTP/1.1 200
Content-Type:application/json
{
  "template_id" : "String"
}
```

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|template\_id|String|ba1fe77b-b01e-4640-b77e-8f1b80e3e3cf|The ID of the template. |

## Examples

Sample requests

```
POST /templates HTTP/1.1 
Content-Type:application/json
{
  "name" : "service-account-template",
  "template" : "apiVersion: v1\\nkind: ServiceAccount\\nmetadata:\\n  name: test-sa",
  "tags" : "test",
  "description" : "this is test",
  "template_type" : "kubernetes"
}
```

Sample success responses

`XML` format

```
HTTP/1.1 200 OK
Content-Type:application/xml

<template_id>ba1fe77b-b01e-4640-b77e-8f1b80e3e3cf</template_id>
```

`JSON` format

```
HTTP/1.1 200 OK
Content-Type:application/json

{
  "template_id" : "ba1fe77b-b01e-4640-b77e-8f1b80e3e3cf"
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

