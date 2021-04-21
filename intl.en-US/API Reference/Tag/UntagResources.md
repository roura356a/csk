# UntagResources

Removes the labels of specified Container Service for Kubernetes \(ACK\) clusters.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=UntagResources&type=ROA&version=2015-12-15)

## Request syntax

```
DELETE /tags?region_id=String&resource_ids=["String"]&resource_type=String&tag_keys=["String"] HTTP/1.1
Content-Type:application/json
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|region\_id|String|Yes|cn-hangzhou|The ID of the region where the resources are deployed. |
|resource\_ids|Array of String|Yes|c1a7bb282fc0d48b593\*\*\*|The IDs of the resources. You can specify up to 50 resources. |
|resource\_type|String|Yes|CLUSTER|The type of the resources. Valid value: `CLUSTER`. |
|tag\_keys|Array of String|Yes|TestKey1|The keys of the labels that you want to remove. You can specify up to 20 keys. |

## Response syntax

```
HTTP/1.1 200 OK
```

## Response parameters

None

## Examples

Sample requests

```
DELETE /tags?region_id=cn-hangzhou&resource_ids=["c1a7bb282fc0d48b593***"]&resource_type=CLUSTER&tag_keys=["TestKey1"] HTTP/1.1 
Content-Type:application/json
```

Sample success responses

`JSON` format

```
HTTP/1.1 200 OK
```

## Error code

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

