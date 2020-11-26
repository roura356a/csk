# Query application deployment templates

You can call DescribeTemplates to query application deployment templates.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DescribeTemplates&type=ROA&version=2015-12-15)

## Request parameter types

This operation uses only common request parameters. For more information, see Common parameters.

## Request syntax

```
get /templates http|https
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|template\_type|String|No|kubernetes|The type of the template. Valid values: kubernetes and compose. Default value: kubernetes. |

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|page\_info|Object| |The pagination details. |
|page\_number|Long|"20"|The total number of pages returned. |
|page\_size|Long|"3"|The number of entries returned on each page. |
|total\_count|Long|"50"|The total number of entries returned. |
|templates|Array of templates| |A list of templates. |
|acl|String|"private"|Indicates whether the template is private, public, or shared by specified users. Valid values:

 -   private
-   public
-   shared

 Default value: private. |
|created|String|"2020-06-10T16:30:16+08:00"|The time when the template was created. |
|description|String|"a web server"|The description of the template. |
|id|String|"874ec485-e7e6-4373-8a3b-47bde8ae789f"|The ID of the template. |
|name|String|"webserver"|The name of the template. |
|tags|String|"kubernetes"|The tags of the template. By default, the value is the name of the template. |
|template|String|"apiVersion: apps/v1\\\\nkind: Deployment\\\\nmetadata:\\\\n name: nginx-deployment-basic\\\\n labels:\\\\n app: nginx\\\\nspec:\\\\n replicas: 2\\\\n selector:\\\\n matchLabels:\\\\n app: nginx\\\\n template:\\\\n metadata:\\\\n labels:\\\\n app: nginx\\\\n spec:\\\\n containers:\\\\n - name: nginx\\\\n image: busybox:latest\\\\n ports:\\\\n - containerPort: 80"|The content of the template. |
|template\_type|String|"kubernetes"|The type of the template. Valid values:

 -   kubernetes: the standard template provided by Kubernetes.
-   compose: the standard template provided by Docker Compose. |
|updated|String|"2020-06-10T16:30:16+08:00"|The time when the template was updated. |

## Examples

Sample requests

```
GET /templates HTTP/1.1
Common request parameters
```

Sample success responses

`XML` format

```
<page_info>
    <page_number>20</page_number>
    <total_count>50</total_count>
    <page_size>3</page_size>
</page_info>
<templates>
    <template>apiVersion: apps/v1\nkind: Deployment\nmetadata:\n  name: nginx-deployment-basic\n  labels:\n    app: nginx\nspec:\n  replicas: 2\n  selector:\n    matchLabels:\n      app: nginx\n  template:\n    metadata:\n      labels:\n        app: nginx\n    spec:\n      containers:\n      - name: nginx\n        image: busybox:latest\n        ports:\n        - containerPort: 80</template>
    <created>2020-06-10T16:30:16+08:00</created>
    <name>webserver</name>
    <description>a web server</description>
    <template_type>kubernetes</template_type>
    <acl>private</acl>
    <id>874ec485-e7e6-4373-8a3b-47bde8ae789f</id>
    <updated>2020-06-10T16:30:16+08:00</updated>
    <tags>kubernetes</tags>
</templates>
```

`JSON` format

```
{
    "page_info": {
        "page_number": "20",
        "total_count": "50",
        "page_size": "3"
    },
    "templates": [
        {
            "template": "apiVersion: apps/v1\\nkind: Deployment\\nmetadata:\\n  name: nginx-deployment-basic\\n  labels:\\n    app: nginx\\nspec:\\n  replicas: 2\\n  selector:\\n    matchLabels:\\n      app: nginx\\n  template:\\n    metadata:\\n      labels:\\n        app: nginx\\n    spec:\\n      containers:\\n      - name: nginx\\n        image: busybox:latest\\n        ports:\\n        - containerPort: 80",
            "created": "2020-06-10T16:30:16+08:00",
            "name": "webserver",
            "description": "a web server",
            "template_type": "kubernetes",
            "acl": "private",
            "id": "874ec485-e7e6-4373-8a3b-47bde8ae789f",
            "updated": "2020-06-10T16:30:16+08:00",
            "tags": "kubernetes"
        }
    ]
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

