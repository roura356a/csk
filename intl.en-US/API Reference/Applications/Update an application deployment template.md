# Update an application deployment template

You can call UpdateTemplate to update an application deployment template.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=UpdateTemplate&type=ROA&version=2015-12-15)

## Request parameter types

This operation uses only common request parameters. For more information, see Common parameters.

## Request syntax

```
put /templates/{TemplateId} http|https
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|TemplateId|String|Yes|72d20cf8-a533-4ea9-a10d-e7630d3d2708|The ID of the template. |
|name|String|No|webserver01|The name of the template. |
|template|String|No|apiVersion: apps/v1\\\\nkind: Deployment\\\\nmetadata:\\\\n name: nginx-deployment-basic\\\\n labels:\\\\n app: nginx\\\\nspec:\\\\n replicas: 2\\\\n selector:\\\\n matchLabels:\\\\n app: nginx\\\\n template:\\\\n metadata:\\\\n labels:\\\\n app: nginx\\\\n spec:\\\\n containers:\\\\n - name: nginx\\\\n image: busybox:latest\\\\n ports:\\\\n - containerPort: 8080|The content of the template. |
|tags|String|No|web|The tags of the template. |
|description|String|No|web server cluster|The description of the template. |
|template\_type|String|No|kubernetes|The type of the template. Valid values:

 -   kubernetes: the standard template provided by Kubernetes.
-   compose: the standard template provided by Docker Compose. |

## Examples

Sample requests

```
PUT /templates/[TemplateId] HTTP/1.1
Common request parameters
{
    "TemplateId":"72d20cf8-a533-4ea9-a10d-e7630d3d2708",
    "name":"webserver01",
    "template_type":"web server cluster",
    "template":"apiVersion: apps/v1\\nkind: Deployment\\nmetadata:\\n  name: nginx-deployment-basic\\n  labels:\\n    app: nginx\\nspec:\\n  replicas: 2\\n  selector:\\n    matchLabels:\\n      app: nginx\\n  template:\\n    metadata:\\n      labels:\\n        app: nginx\\n    spec:\\n      containers:\\n      - name: nginx\\n        image: busybox:latest\\n        ports:\\n        - containerPort: 8080",
    "tags":"web",
    "description":"kubernetes"
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

