# ScaleClusterNodePool

Scales out a node pool.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=ScaleClusterNodePool&type=ROA&version=2015-12-15)

## Request syntax

```
POST /clusters/ClusterId/nodepools/NodepoolId HTTP/1.1 
Content-Type:application/json
{
  "count" : Long
}
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|c23421cfa74454bc8b37163fd19af\*\*\*\*|The ID of the ACK cluster. |
|NodepoolId|String|Yes|np31da1b38983f4511b490fc62108a\*\*\*\*|The ID of the node pool. |

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|count|Long|No|1|The number of nodes that you want to add to the node pool. You can add up to 500 nodes in one API call. The maximum number of nodes that can be added is limited by the quota of nodes in the cluster. |

## Response syntax

```
HTTP/1.1 200
Content-Type:application/json
{
  "task_id" : "String"
}
```

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|task\_id|String|T-5faa48fb31b6b8078d00\*\*\*\*|The ID of the scaling task. |

## Examples

Sample responses

```
POST /clusters/c23421cfa74454bc8b37163fd19af****/nodepools/np31da1b38983f4511b490fc62108a**** HTTP/1.1 
Content-Type:application/json
{
  "count" : 1
}
```

Sample success responses

`XML` format

```
HTTP/1.1 200 OK
Content-Type:application/xml

<task_id>T-5faa48fb31b6b8078d00****</task_id>
```

`JSON` format

```
HTTP/1.1 200 OK
Content-Type:application/json

{
  "task_id" : "T-5faa48fb31b6b8078d00****"
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

