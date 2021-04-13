# DeleteClusterNodepool

Deletes a node pool.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DeleteClusterNodepool&type=ROA&version=2015-12-15)

## Request syntax

```
DELETE /clusters/ClusterId/nodepools/NodepoolId HTTP/1.1
Content-Type:application/json
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|c61da77e8bfbc4c4c999af2b51b65\*\*\*\*|The ID of the ACK cluster. |
|NodepoolId|String|Yes|np30db56bcac7843dca90b999c8928\*\*\*\*|The ID of the node pool. |

## Response syntax

```
HTTP/1.1 200 OK
```

## Response parameters

None

## Examples

Sample requests

```
DELETE /clusters/c61da77e8bfbc4c4c999af2b51b65****/nodepools/np30db56bcac7843dca90b999c8928**** HTTP/1.1 
Content-Type:application/json
```

Sample success responses

`JSON` format

```
HTTP/1.1 200 OK
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

