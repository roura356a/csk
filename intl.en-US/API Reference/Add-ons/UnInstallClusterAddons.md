# UnInstallClusterAddons

Uninstalls components from a specified Container Service for Kubernetes \(ACK\) cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=UnInstallClusterAddons&type=ROA&version=2015-12-15)

## Request syntax

```
POST /clusters/ClusterId/components/uninstall HTTP/1.1
Content-Type:application/json

[ {
  "name" : "String"
} ]
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|c5b5e80b0b64a4bf6939d2d8fbbc5\*\*\*\*|The ID of the ACK cluster. |

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
| |Array|No| |The list of components that you want to uninstall. The list is an array. |
|name|String|No|ack-node-problem-detector|The name of the component. |

## Response syntax

```
HTTP/1.1 200 OK
```

## Response parameters

None

## Examples

Sample requests

```
POST /clusters/c5b5e80b0b64a4bf6939d2d8fbbc5****/components/uninstall HTTP/1.1 
Host:cs.aliyuncs.com 
Content-Type:application/json

[ {
  "name" : "ack-node-problem-detector"
} ]
```

Sample success responses

`JSON` format

```
HTTP/1.1 200 OK
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

