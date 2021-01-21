# Create an application deployment template

You can call CreateTemplate to create an application deployment template.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=CreateTemplate&type=ROA&version=2015-12-15)

## Request parameter types

This operation uses common request parameters only. For more information, see Common parameters.

## Request syntax

```
POST|PUT /templates HTTPS|HTTP
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|name|String|Yes|nginx|The name of the template. |
|template|String|Yes|apiVersion: apps/v1\\nkind: Deployment\\nmetadata:\\n name: nginx-deployment-basic\\n labels:\\n app: nginx\\nspec:\\n replicas: 2\\n selector:\\n matchLabels:\\n app: nginx\\n template:\\n metadata:\\n labels:\\n app: nginx\\n spec:\\n containers:\\n - name: nginx\\n image: busybox:latest\\n ports:\\n - containerPort: 80|The content of the template. |
|tags|String|No|common|The type of the tags. Valid values: private and common. |
|template\_type|String|No|kubernetes|The type of the template. |

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|template\_id|String|20663627-81ee-4cfe-9b69-c96fcb84a674|The ID of the template. |

## Examples

Sample requests

```
POST /templates HTTP/1.1
Common request parameters
{
"name": "nginx",
"template": "apiVersion: apps/v1\nkind: Deployment\nmetadata:\n  name: nginx-deployment-basic\n  labels:\n    app: nginx\nspec:\n  replicas: 2\n  selector:\n    matchLabels:\n      app: nginx\n  template:\n    metadata:\n      labels:\n        app: nginx\n    spec:\n      containers:\n      - name: nginx\n        image: busybox:latest\n        ports:\n        - containerPort: 80"
}
```

Sample success responses

`XML` format

```
<template_id>20663627-81ee-4cfe-9b69-c96fcb84a674</template_id>
```

`JSON` format

```
{"template_id":"20663627-81ee-4cfe-9b69-c96fcb84a674"}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

