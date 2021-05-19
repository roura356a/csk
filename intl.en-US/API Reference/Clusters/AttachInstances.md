# AttachInstances

Adds existing Edge Node Service \(ENS\) nodes to a specified managed edge Kubernetes cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer automatically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=AttachInstances&type=ROA&version=2015-12-15)

## Request parameters

This operation uses only common request parameters. For more information, see [Common request parameters](Common request parameterst1884803.dita#concept_944293).

## Request syntax

```
POST /clusters/{ClusterId}/attach 
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|cluster\_id|String|Yes|Cccfd68c474454665ace07efce924\*\*\*\*|The ID of the ACK cluster.|
|instances|Array|Yes|\["i-5j443uf\*\*\*\*"\]|An array that specifies the existing ENS nodes that you want to add.|
|is\_edge\_worker|Boolean|Yes|true|Specifies whether to add the ENS nodes as worker nodes. Valid values:-   `true`: adds the ENS nodes as worker nodes.
-   `false`: does not add the ENS nodes as worker nodes.

If you want to add the ENS nodes to a managed edge Kubernetes cluster, you must set this parameter to `true`. |

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|list|Array of list| |The details of the added nodes.|
|code|String|200|The code that indicates the task result.|
|instanceId|String|i-5j2zjis\*\*\*\*|The IDs of the ENS instances that are added to the ACK cluster.|
|message|String|successful|Indicates whether the ENS instances are successfully added to the ACK cluster.|
|task\_id|String|T-5a544aff80282e39ea000039|The ID of the task.|

## Examples

Sample requests

```
POST /clusters/Cccfd68c474454665ace07efce924****/attach
<Common request parameters>
{
    "instances":[
        "i-wz9e8pvnjalxkggk****"
    ],
    "is_edge_worker":true
}
```

Sample responses

```
{
  "list": [
    {
      "code": "200",
      "instanceId": "i-5j2zjis****",
      "message": "successful"
    },
    {
      "code": "200",
      "instanceId": "i-5j443uf****",
      "message": "successful"
    }
  ],
  "task_id": "T-5d6f733d9e408ec74f000002"
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

