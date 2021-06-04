# DescribeTemplateAttribute

Queries the details of a specified application deployment template.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DescribeTemplateAttribute&type=ROA&version=2015-12-15)

## Request syntax

```
GET /templates/TemplateId?template_type=String HTTP/1.1 
Content-Type:application/json
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|TemplateId|String|Yes|874ec485-e7e6-4373-8a3b-47bde8a\*\*\*\*|The ID of the template. |

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|template\_type|String|No|kubernetes|The type of the template. You can set the parameter to a custom value.

 -   If you set the parameter to `kubernetes`, the template is displayed on the Templates page in the Container Service for Kubernetes \(ACK\) console.
-   If you set the parameter to `compose`, the template is displayed on the Container Service - Swarm page in the console. However, Container Service for Swarm is deprecated.
-   If you set the parameter to a value other than `kubernetes`, the template is not displayed on the Templates page in the ACK console. We recommend that you set the parameter to `kubernetes`.

 Default value: `kubernetes`. |

## Response syntax

```
HTTP/1.1 200
Content-Type:application/json
[ {
  "id" : "String",
  "acl" : "String",
  "name" : "String",
  "template" : "String",
  "template_type" : "String",
  "description" : "String",
  "tags" : "String",
  "template_with_hist_id" : "String",
  "created" : "String",
  "updated" : "String"
} ]
```

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
| |Array of template\_info| |The details of the queried template. |
|id|String|72d20cf8-a533-4ea9-a10d-e7630d3d\*\*\*\*|The ID of the template. When you update a template, a new template ID is generated. |
|acl|String|private|The access control policy of the template. |
|name|String|web|The name of the template. |
|template|String|apiVersion: V1\\n\*\*\*|The template content in YAML format. |
|template\_type|String|kubernetes|The type of the template. The value can be a custom value.

 -   If the value is `kubernetes`, the template is displayed on the Templates page in the ACK console.
-   If the value is `compose`, the template is displayed on the Container Service - Swarm page in the console. However, Container Service for Swarm has stopped providing services.
-   If the value of the parameter is not `kubernetes`, the template is not displayed on the Templates page in the ACK console. We recommend that you set the parameter to `kubernetes`.

 Default value: `kubernetes`. |
|description|String|test template|The description of the template. |
|tags|String|sa|The tags of the template. |
|template\_with\_hist\_id|String|874ec485-e7e6-4373-8a3b-47bde8ae\*\*\*\*|The unique ID of the template. The value remains unchanged after the template is updated. |
|created|String|2020-09-16T19:21:29+08:00|The time when the template was created. |
|updated|String|2020-09-16T19:21:29+08:00|The time when the template was updated. |

## Examples

Sample requests

```
GET /templates/874ec485-e7e6-4373-8a3b-47bde8a****?template_type=kubernetes HTTP/1.1 
Content-Type:application/json
```

Sample success responses

`XML` format

```
HTTP/1.1 200 OK
Content-Type:application/xml

<0>
    <id>72d20cf8-a533-4ea9-a10d-e7630d3d****</id>
    <acl>private</acl>
    <name>web</name>
    <template>apiVersion: V1\n***</template>
    <template_type>kubernetes</template_type>
    <description>test template</description>
    <tags>sa</tags>
    <template_with_hist_id>874ec485-e7e6-4373-8a3b-47bde8ae****</template_with_hist_id>
    <created>2020-09-16T19:21:29+08:00</created>
    <updated>2020-09-16T19:21:29+08:00</updated>
</0>
```

`JSON` format

```
HTTP/1.1 200 OK
Content-Type:application/json

[ {
  "id" : "72d20cf8-a533-4ea9-a10d-e7630d3d****",
  "acl" : "private",
  "name" : "web",
  "template" : "apiVersion: V1\\n***",
  "template_type" : "kubernetes",
  "description" : "test template",
  "tags" : "sa",
  "template_with_hist_id" : "874ec485-e7e6-4373-8a3b-47bde8ae****",
  "created" : "2020-09-16T19:21:29+08:00",
  "updated" : "2020-09-16T19:21:29+08:00"
} ]
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

