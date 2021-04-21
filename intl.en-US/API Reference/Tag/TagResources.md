# TagResources

Adds labels to a specified Container Service for Kubernetes \(ACK\) cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=TagResources&type=ROA&version=2015-12-15)

## Request syntax

```
PUT /tags HTTP/1.1
Content-Type:application/json

{
  "resource_ids" : [ "String" ],
  "resource_type" : "String",
  "region_id" : "String",
  "tags" : [ {
    "key" : "String",
    "value" : "String"
  } ]
}
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|resource\_ids|Array of String|Yes|c1a7bb282fc0d48b\*\*\*\*|The IDs of the resources to which you want to add labels. You can specify up to 50 resources. |
|resource\_type|String|Yes|CLUSTER|The type of the resources. Valid value: `CLUSTER`. |
|region\_id|String|Yes|cn-hangzhou|The ID of the region where the resources are deployed. |
|tags|Array of [tag](/intl.en-US/API Reference/Commonly used parameters.md)|Yes| |The key-value pairs that you want to add to the resources. You can add up to 20 key-value pairs. Note:

 -   A value cannot be empty and can contain up to 128 characters.
-   Neither a key or a value can start with `aliyun` or `acs:`.
-   Neither a key or a value can contain `http://` or `https://`. |

## Response syntax

```
HTTP/1.1 200 OK
```

## Response parameters

None

## Examples

Sample requests

```
PUT /tags HTTP/1.1 
Content-Type:application/json
{
  "resource_ids" : [ "c1a7bb282fc0d48b****" ],
  "resource_type" : "CLUSTER",
  "region_id" : "cn-hangzhou",
  "tags" : [ {
    "key" : "env",
    "value" : "prod"
  } ]
}
```

Sample success responses

`JSON` format

```
HTTP/1.1 200 OK
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

