# Query tags of resources

You can call ListTagResources to query tags that are attached to resources.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=ListTagResources&type=ROA&version=2015-12-15)

## Request parameter types

This operation uses only common request parameters. For more information, see Common parameters.

## Request syntax

```
get /tags http|https
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|next\_token|String|No|\*\*\*|The token that you can use to start the next query. |
|resource\_ids|String|Yes|\["xxxxx","xxxxxx"\]|The IDs of the resources to query. |
|tags|String|No|\[\{\\"key\\":\\"env\\",\\"value\\",\\"dev\\"\},\{\\"key\\":\\"dev\\", \\"value\\":\\"IT\\"\}\]|The tags to query. Set the value to a JSON string that contains a maximum of 20 key-value pairs. |
|resource\_type|String|Yes|SLB|The type of the resource. |

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|next\_token|String|"\*\*\*"|The token used to start the next query. |
|request\_id|String|"E368C761-F8F6-4A36-9B58-BD53D5CD0CEB"|The ID of the request. |
|tag\_resources|Object| |The tags of the resources. |
|tag\_resource|Object| |The tags of the resource. |
|resource\_id|String|"c23421cfa74454bc8b37163fd19af\*\*\*\*"|The ID of the resource. |
|resource\_type|String|"ALIYUN::CS::CLUSTER"|The type of the resource. |
|tag\_key|String|"ack.aliyun.com"|The key of the tag. |
|tag\_value|String|"c23421cfa74454bc8b37163fd19af\*\*\*\*"|The value of the tag. |

## Examples

Sample requests

```
GET /tags HTTP/1.1
Common request parameters
{
    "resource_type":"CLUSTER",
    "resource_ids":[
        "xxx",
        "yyy"
    ],
    "tags":[
        {
            "key":"env",
            "value":"dev"
        },
        {
            "key":"dep",
            "value":"IT"
        }
    ]
}
```

Sample success responses

`XML` format

```
<next_token/>
<request_id>E368C761-F8F6-4A36-9B58-BD53D5CD0CEB</request_id>
<tag_resources>
    <tag_resource>
        <tag_key>ack.aliyun.com</tag_key>
        <tag_value>c23421cfa74454bc8b37163fd19af****</tag_value>
        <resource_id>c23421cfa74454bc8b37163fd19af****</resource_id>
        <resource_type>ALIYUN::CS::CLUSTER</resource_type>
    </tag_resource>
    <tag_resource>
        <tag_key>acs:rm:rgId</tag_key>
        <tag_value>rg-acfmyvw3wjm****</tag_value>
        <resource_id>c23421cfa74454bc8b37163fd19af****</resource_id>
        <resource_type>ALIYUN::CS::CLUSTER</resource_type>
    </tag_resource>
</tag_resources>
```

`JSON` format

```
{
    "next_token":"",
    "request_id":"E368C761-F8F6-4A36-9B58-BD53D5CD0CEB",
    "tag_resources":{
        "tag_resource":[
            {
                "tag_key":"ack.aliyun.com",
                "tag_value":"c23421cfa74454bc8b37163fd19af****",
                "resource_id":"c23421cfa74454bc8b37163fd19af****",
                "resource_type":"ALIYUN::CS::CLUSTER"
            },
            {
                "tag_key":"acs:rm:rgId",
                "tag_value":"rg-acfmyvw3wjm****",
                "resource_id":"c23421cfa74454bc8b37163fd19af****",
                "resource_type":"ALIYUN::CS::CLUSTER"
            }
        ]
    }
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

