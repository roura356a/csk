# Delete a node pool

You can call the DeleteClusterNodepool operation to delete a node pool.

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
|ClusterId|String|Yes|c61da77e8bfbc4c4c999af2b51b65\*\*\*\*|The ID of the Container Service for Kubernetes \(ACK\) cluster that you want to manage. |
|NodepoolId|String|Yes|np30db56bcac7843dca90b999c8928\*\*\*\*|The ID of the node pool that you want to delete. |

## Response syntax

```
HTTP/1.1 200 OK
```

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|request\_id|String|7263C978-3DBD-4E06-B319-793B38A2F388|The ID of the request. |

## Examples

Sample requests

```
DELETE /clusters/c61da77e8bfbc4c4c999af2b51b65****/nodepools/np30db56bcac7843dca90b999c8928**** HTTP/1.1 
Content-Type:application/json
```

Sample success responses

`XML` format

```
HTTP/1.1 200 OK
Content-Type:application/xml

<DeleteClusterNodepoolResponse>
    <request_id>7263C978-3DBD-4E06-B319-793B38A2F388</request_id>
</DeleteClusterNodepoolResponse>
```

`JSON` format

```
HTTP/1.1 200 OK
Content-Type:application/json

{
  "request_id" : "7263C978-3DBD-4E06-B319-793B38A2F388"
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

