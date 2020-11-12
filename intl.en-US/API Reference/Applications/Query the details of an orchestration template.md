# Query the details of an orchestration template

You can call DescribeTemplateAttribute to query the details of an orchestration template.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DescribeTemplateAttribute&type=ROA&version=2015-12-15)

## Request parameter types

This operation uses only common request parameters. For more information, see Common parameters.

## Request syntax

```
get /templates/{TemplateId} http|https
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|TemplateId|String|Yes|874ec485-e7e6-4373-8a3b-47bde8a\*\*\*\*|The ID of the template. |

GET /templates/\{TemplateId\} HTTP/1.1

Common request parameters

\{

"TemplateId":"874ec485-e7e6-4373-8a3b-47bde8a****"

\}

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
| |Array of data| |The response body. |
|acl|String|"private"|The access control policy of the orchestration template. |
|created|String|"2020-09-16T19:21:29+08:00"|The time when the orchestration template was created. |
|description|String|"\*\*\*"|The description of the orchestration template. |
|id|String|"72d20cf8-a533-4ea9-a10d-e7630d3d2708"|The ID of the orchestration template. If you update a template, a new template ID is generated. |
|name|String|"web"|The name of the orchestration template. |
|template|String|"\*\*\*"|The YAML content of the orchestration template. |
|template\_hash\_code\_version|String|"0d6983a3c8169e570bf3f268396\*\*\*\*"|The ID of the orchestration template. You can use the value in Application Center. |
|template\_type|String|"kubernetes"|The type of the orchestration template. Default value: kubernetes. |
|template\_with\_hist\_id|String|"874ec485-e7e6-4373-8a3b-47bde8ae789f"|The unique ID of the orchestration template. The value remains unchanged after a template update. |
|updated|String|"2020-09-16T19:21:29+08:00"|The time when the orchestration template was updated. |

## Examples

Sample requests

```

```

Sample success responses

`XML` format

```
<0>
    <tags>aa</tags>
    <template/>
    <id>72d20cf8-a533-4ea9-a10d-e7630d3d****</id>
    <template_with_hist_id>874ec485-e7e6-4373-8a3b-47bde8ae789f</template_with_hist_id>
    <updated>2020-09-16T19:21:29+08:00</updated>
    <created>2020-09-16T19:21:29+08:00</created>
    <acl>private</acl>
    <description>bbbbb</description>
    <name>web045555</name>
    <template_hash_code_version>0d6983a3c8169e570bf3f268396a****</template_hash_code_version>
    <template_type>kubernetes</template_type>
</0>
```

`JSON` format

```
[
    {
        "tags":"aa",
        "template":"",
        "id":"72d20cf8-a533-4ea9-a10d-e7630d3d****",
        "template_with_hist_id":"874ec485-e7e6-4373-8a3b-47bde8ae789f",
        "updated":"2020-09-16T19:21:29+08:00",
        "created":"2020-09-16T19:21:29+08:00",
        "acl":"private",
        "description":"bbbbb",
        "name":"web045555",
        "template_hash_code_version":"0d6983a3c8169e570bf3f268396a****",
        "template_type":"kubernetes"
    }
]
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

