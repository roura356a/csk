# ModifyClusterTags

Modifies the labels of a specified Container Service for Kubernetes \(ACK\) cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=ModifyClusterTags&type=ROA&version=2015-12-15)

## Request syntax

```
POST /clusters/ClusterId/tags HTTP/1.1
Content-Type:application/json

[ {
  "key" : "String",
  "value" : "String"
} ]
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|c106f377e16f34eb1808d6b9362c9\*\*\*\*|The ID of the ACK cluster. |

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
| |Array of [tag](/intl.en-US/API Reference/Commonly used parameters.md)|No| |The data of the labels that you want to modify. |

## Response syntax

```
HTTP/1.1 200 OK
```

## Response parameters

None

## Examples

Sample requests

```
POST /clusters/c106f377e16f34eb1808d6b9362c9****/tags HTTP/1.1 
Content-Type:application/json
[ {
  "key" : "env",
  "value" : "prod"
} ]
```

Sample success responses

`JSON` format

```
HTTP/1.1 200 OK
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

