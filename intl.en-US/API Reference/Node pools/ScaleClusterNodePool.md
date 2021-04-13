# ScaleClusterNodePool

Scales out a node pool.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=ScaleClusterNodePool&type=ROA&version=2015-12-15)

## Request headers

This operation uses only common request headers. For more information, see the Common request parameters topic.

## Request syntax

```
POST /clusters/{ClusterId}/nodepools/{NodepoolId} 
```

## Request parameters

|Parameter|Type|Position|Required|Example|Description|
|---------|----|--------|--------|-------|-----------|
|ClusterId|String|Path|Yes|c23421cfa74454bc8b37163fd19af\*\*\*\*|The ID of the ACK cluster. |
|NodepoolId|String|Path|Yes|np31da1b38983f4511b490fc62108a\*\*\*\*|The ID of the node pool. |
| |Object|Body|No| |The request body. |
|count|Long|Body|No|1|The number of nodes to be added to the node pool. |

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|task\_id|String|T-5faa48fb31b6b8078d000006|The ID of the task. |

## Examples

Sample requests

```
POST /clusters/c23421cfa74454bc8b37163fd19af****/nodepools/np31da1b38983f4511b490fc62108a**** HTTP/1.1 
Common request header
{
	"count":"1"
}
```

Sample success responses

`XML` format

```
<task_id>T-5faa48fb31b6b8078d000006</task_id>
```

`JSON` format

```
{
    "task_id": "T-5faa48fb31b6b8078d000006"
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

