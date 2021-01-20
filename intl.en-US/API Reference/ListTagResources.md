# ListTagResources

Queries tags that are added to the specified resources.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=ListTagResources&type=ROA&version=2015-12-15)

## Request headers

This operation uses only common request headers. For more information, see the Common request parameters topic.

## Request syntax

```
GET /tags 
```

## Request parameters

|Parameter|Type|Position|Required|Example|Description|
|---------|----|--------|--------|-------|-----------|
|resource\_ids|Array of String|Query|Yes|c71cf3d796c374bf48644482cb0c3\*\*\*\*|The ID of the cluster. |
|tags|Array|Query|No|\[\{\\"key\\":\\"env\\",\\"value\\",\\"dev\\"\},\{\\"key\\":\\"dev\\", \\"value\\":\\"IT\\"\}\]|The list of tags to be queried. You can specify up to 20 subitems. |
|key|String|Query|No|ack.aliyun.com|The key of the tag to be queried. |
|value|String|Query|No|c71cf3d796c374bf48644482cb0c3\*\*\*\*|The value of the tag to be queried. |
|next\_token|String|Query|No|\*\*\*|The token that is used to start the next query. |

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|next\_token|String|\*\*\*\*\*|The token that is used to start the next query. |
|request\_id|String|E368C761-F8F6-4A36-9B58-BD53D5CD0CEB|The ID of the request. |
|tag\_resources|object| |The tags of the queried resources. |
|tag\_resource|Array of tag\_resource| |The tags of the queried resource. |
|tag\_key|String|ack.aliyun.com|The key of the returned tag. |
|tag\_value|String|c71cf3d796c374bf48644482cb0c3\*\*\*\*|The value of the returned tag. |
|resource\_id|String|i-xxx|The ID of the resource. |
|resource\_type|String|CLUSTER|The type of the resource. |

## Examples

Sample requests

```
GET /tags HTTP/1.1 
Common request header
{
    "resource_ids":[
        "c71cf3d796c374bf48644482cb0c3****"
    ],
    "next_token":"***",
    "tags":[
        {
            "value":"c71cf3d796c374bf48644482cb0c3****",
            "key":"ack.aliyun.com"
        }
    ]
}
```

Sample success responses

`XML` format

```
<tag_resources>
    <tag_resource>
        <tag_value>c71cf3d796c374bf48644482cb0c3****</tag_value>
        <resource_type>CLUSTER</resource_type>
        <resource_id>i-xxx</resource_id>
        <tag_key>ack.aliyun.com</tag_key>
    </tag_resource>
</tag_resources>
<next_token>*****</next_token>
<request_id>E368C761-F8F6-4A36-9B58-BD53D5CD0CEB</request_id>
```

`JSON` format

```
{
  "tag_resources": {
    "tag_resource": [
      {
        "tag_value": "c71cf3d796c374bf48644482cb0c3****",
        "resource_type": "CLUSTER",
        "resource_id": "i-xxx",
        "tag_key": "ack.aliyun.com"
      }
    ]
  },
  "next_token": "*****",
  "request_id": "E368C761-F8F6-4A36-9B58-BD53D5CD0CEB"
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

