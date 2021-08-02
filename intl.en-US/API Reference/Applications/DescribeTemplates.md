# DescribeTemplates

Queries all application deployment templates.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DescribeTemplates&type=ROA&version=2015-12-15)

## Request syntax

```
GET /templates?template_type=String&page_num=Long&page_size=Long HTTP/1.1 
Content-Type:application/json
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|template\_type|String|Yes|kubernetes|The type of the templates that you want to query. You can set the parameter to a custom value.

 -   If you set the parameter to `kubernetes`, the template is displayed on the Templates page in the Container Service for Kubernetes \(ACK\) console.
-   If you set the parameter to `compose`, the template is not displayed on the Templates page in the ACK console.

 Default value: `kubernetes`. |
|page\_num|Long|No|1|The number of the page to return.

 Default value: 1. |
|page\_size|Long|No|10|The number of entries to return on each page.

 Default value: 10. |

## Response syntax

```
HTTP/1.1 200
Content-Type:application/json
{
  "templates" : [ {
    "acl" : "String",
    "id" : "String",
    "name" : "String",
    "description" : "String",
    "tags" : "String",
    "template" : "String",
    "template_type" : "String",
    "created" : "String",
    "updated" : "String",
    "template_with_hist_id" : "String"
  } ],
  "page_info" : {
    "page_number" : Long,
    "page_size" : Long,
    "total_count" : Long
  }
}
```

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|templates|Array of template| |The list of the templates returned . |
|acl|String|private|The access control policy of the template. Valid values:

 -   `private`: The template is private.
-   `public`: The template is public.
-   `shared`: The template can be shared.

 Default value: `private`. |
|id|String|874ec485-e7e6-4373-8a3b-47bde8ae789f|The ID of the template. |
|name|String|webserver|The name of the template. |
|description|String|a web server|The description of the template. |
|tags|String|kubernetes|The tag of the template. By default, the value is the name of the template. |
|template|String|apiVersion: apps/v1\\nkind: Deployment\\nmetadata:\\n name: nginx-deployment-basic\\n labels:\\n app: nginx\\nspec:\\n replicas: 2\\n selector:\\n matchLabels:\\n app: nginx\\n template:\\n metadata:\\n labels:\\n app: nginx\\n spec:\\n containers:\\n - name: nginx\\n image: busybox:latest\\n ports:\\n - containerPort: 80|The template content in YAML format. |
|template\_type|String|kubernetes|The type of the template. The value can be a custom value.

 -   If the value is `kubernetes`, it indicates that the template is displayed on the Templates page in the ACK console.
-   If the value is `compose`, it indicates that the template is displayed on the Container Service - Swarm page in the console. However, Container Service for Swarm is deprecated. |
|created|String|2020-06-10T16:30:16+08:00|The time when the template was created. |
|updated|String|2020-06-10T16:30:16+08:00|The time when the template was updated. |
|template\_with\_hist\_id|String|ad81d115-7c8b-47e7-a222-9c28d7f6e588|The ID of the parent template. The value of `template_with_hist_id` is the same for each template version. This allows you to manage different template versions. |
|page\_info|object| |The pagination details. |
|page\_number|Long|20|The page number of the returned page. |
|page\_size|Long|3|The maximum number of entries returned per page. |
|total\_count|Long|50|The total number of entries returned. |

## Examples

Sample requests

```
GET /templates?template_type=kubernetes&page_num=1&page_size=10 HTTP/1.1 
Content-Type:application/json
```

Sample success responses

`XML` format

```
HTTP/1.1 200 OK
Content-Type:application/xml

<templates>
    <acl>private</acl>
    <id>874ec485-e7e6-4373-8a3b-47bde8ae789f</id>
    <name>webserver</name>
    <description>a web server</description>
    <tags>kubernetes</tags>
    <template>apiVersion: apps/v1\nkind: Deployment\nmetadata:\n  name: nginx-deployment-basic\n  labels:\n    app: nginx\nspec:\n  replicas: 2\n  selector:\n    matchLabels:\n      app: nginx\n  template:\n    metadata:\n      labels:\n        app: nginx\n    spec:\n      containers:\n      - name: nginx\n        image: busybox:latest\n        ports:\n        - containerPort: 80</template>
    <template_type>kubernetes</template_type>
    <created>2020-06-10T16:30:16+08:00</created>
    <updated>2020-06-10T16:30:16+08:00</updated>
    <template_with_hist_id>ad81d115-7c8b-47e7-a222-9c28d7f6e588</template_with_hist_id>
</templates>
<page_info>
    <page_number>20</page_number>
    <page_size>3</page_size>
    <total_count>50</total_count>
</page_info>
```

`JSON` format

```
HTTP/1.1 200 OK
Content-Type:application/json

{
  "templates" : [ {
    "acl" : "private",
    "id" : "874ec485-e7e6-4373-8a3b-47bde8ae789f",
    "name" : "webserver",
    "description" : "a web server",
    "tags" : "kubernetes",
    "template" : "apiVersion: apps/v1\\nkind: Deployment\\nmetadata:\\n  name: nginx-deployment-basic\\n  labels:\\n    app: nginx\\nspec:\\n  replicas: 2\\n  selector:\\n    matchLabels:\\n      app: nginx\\n  template:\\n    metadata:\\n      labels:\\n        app: nginx\\n    spec:\\n      containers:\\n      - name: nginx\\n        image: busybox:latest\\n        ports:\\n        - containerPort: 80",
    "template_type" : "kubernetes",
    "created" : "2020-06-10T16:30:16+08:00",
    "updated" : "2020-06-10T16:30:16+08:00",
    "template_with_hist_id" : "ad81d115-7c8b-47e7-a222-9c28d7f6e588"
  } ],
  "page_info" : {
    "page_number" : 20,
    "page_size" : 3,
    "total_count" : 50
  }
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

