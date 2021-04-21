# ListTagResources

Queries the labels that are added to specified resources.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=ListTagResources&type=ROA&version=2015-12-15)

## Request syntax

```
GET /tags?resource_ids=["String"]&tags=[{"key":"String","value":"String"}]&next_token=String HTTP/1.1 
Content-Type:application/json
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|resource\_ids|Array of String|Yes|c71cf3d796c374bf48644482cb0c3\*\*\*\*|The IDs of the Container Service for Kubernetes \(ACK\) clusters. |
|tags|Array of [tag](/intl.en-US/API Reference/Commonly used parameters.md)|No| |The list of labels that you want to query. You can specify up to 20 labels. |
|next\_token|String|No|\*\*\*|The token that you can use to start the next query. |

## Response syntax

```
HTTP/1.1 200
Content-Type:application/json
{
  "next_token" : "String",
  "request_id" : "String",
  "tag_resources" : {
    "tag_resource" : [ {
      "tag_key" : "String",
      "tag_value" : "String",
      "resource_id" : "String",
      "resource_type" : "String"
    } ]
  }
}
```

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|next\_token|String|c374bf4864448\*\*\*\*|The token that is used to start the next query. |
|request\_id|String|E368C761-F8F6-4A36-9B58-BD53D5CD0CEB|The ID of the request. |
|tag\_resources|object| |The details of the queried labels and resources. |
|tag\_resource|Array of tag\_resource| |The labels of the resource. |
|tag\_key|String|ack.aliyun.com|The name of the label. |
|tag\_value|String|c71cf3d796c374bf48644482cb0c3\*\*\*\*|The value of the label. |
|resource\_id|String|i-xxx|The ID of the resource. |
|resource\_type|String|CLUSTER|The type of the resource. For more information, see [Labels](~~110425~~). |

## Examples

Sample requests

```
GET /tags?resource_ids=["c71cf3d796c374bf48644482cb0c3****"]&tags=[{"key":"env","value":"prod"}]&next_token=*** HTTP/1.1 
Content-Type:application/json
```

Sample success responses

`XML` format

```
HTTP/1.1 200 OK
Content-Type:application/xml

<next_token>c374bf4864448****</next_token>
<request_id>E368C761-F8F6-4A36-9B58-BD53D5CD0CEB</request_id>
<tag_resources>
    <tag_resource>
        <tag_key>ack.aliyun.com</tag_key>
        <tag_value>c71cf3d796c374bf48644482cb0c3****</tag_value>
        <resource_id>i-xxx</resource_id>
        <resource_type>CLUSTER</resource_type>
    </tag_resource>
</tag_resources>
```

`JSON` format

```
HTTP/1.1 200 OK
Content-Type:application/json

{
  "next_token" : "c374bf4864448****",
  "request_id" : "E368C761-F8F6-4A36-9B58-BD53D5CD0CEB",
  "tag_resources" : {
    "tag_resource" : [ {
      "tag_key" : "ack.aliyun.com",
      "tag_value" : "c71cf3d796c374bf48644482cb0c3****",
      "resource_id" : "i-xxx",
      "resource_type" : "CLUSTER"
    } ]
  }
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

