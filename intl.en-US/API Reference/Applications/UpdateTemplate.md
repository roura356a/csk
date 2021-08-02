# UpdateTemplate

Updates an application deployment template.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=UpdateTemplate&type=ROA&version=2015-12-15)

## Request syntax

```
PUT /templates/TemplateId HTTP/1.1 
Content-Type:application/json
{
  "description" : "String",
  "name" : "String",
  "tags" : "String",
  "template" : "String",
  "template_type" : "String"
}
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|TemplateId|String|Yes|72d20cf8-a533-4ea9-a10d-e7630d3d2708|The ID of the template that you want to update. |

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|description|String|No|web server cluster|The details of the template. |
|name|String|No|webserver01|The name of the template. |
|tags|String|No|web|The tag of the template. |
|template|String|No|apiVersion: apps/v1\\\\nkind: Deployment\\\\nmetadata:\\\\n name: nginx-deployment-basic\\\\n labels:\\\\n app: nginx\\\\nspec:\\\\n replicas: 2\\\\n selector:\\\\n matchLabels:\\\\n app: nginx\\\\n template:\\\\n metadata:\\\\n labels:\\\\n app: nginx\\\\n spec:\\\\n containers:\\\\n - name: nginx\\\\n image: busybox:latest\\\\n ports:\\\\n - containerPort: 8080|The template content in YAML format. |
|template\_type|String|No|kubernetes|The type of the template. You can set the parameter to a custom value.

 -   If you set the parameter to `kubernetes`, the template is displayed on the Templates page in the Container Service for Kubernetes \(ACK\) console.
-   If you set the parameter to `compose`, the template is displayed on the Container Service - Swarm page in the console. However, Container Service for Swarm is deprecated. |

## Response syntax

```
HTTP/1.1 200 OK
```

## Response parameters

None

## Examples

Sample requests

```
PUT /templates/72d20cf8-a533-4ea9-a10d-e7630d3d2708 HTTP/1.1 
Content-Type:application/json
{
  "description" : "web server cluster",
  "name" : "webserver01",
  "tags" : "web",
  "template" : "apiVersion: apps/v1\\\\nkind: Deployment\\\\nmetadata:\\\\n  name: nginx-deployment-basic\\\\n  labels:\\\\n    app: nginx\\\\nspec:\\\\n  replicas: 2\\\\n  selector:\\\\n    matchLabels:\\\\n      app: nginx\\\\n  template:\\\\n    metadata:\\\\n      labels:\\\\n        app: nginx\\\\n    spec:\\\\n      containers:\\\\n      - name: nginx\\\\n        image: busybox:latest\\\\n        ports:\\\\n        - containerPort: 8080",
  "template_type" : "kubernetes"
}
```

Sample success responses

`JSON` format

```
HTTP/1.1 200 OK
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

