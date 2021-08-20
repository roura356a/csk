# Remove nodes from node pools

You can call the DeleteClusterNodes operation to remove specified nodes from a Container Service for Kubernetes \(ACK\) cluster.

**Note:**

-   When you remove a node, the pods that run on the node are migrated to other nodes. This may cause service interruptions. We recommend that you remove nodes during off-peak hours.
-   Unknown errors may occur when you remove nodes. Before you remove nodes, back up the data on the nodes.
-   Nodes remain in the unschedulable state when they are being removed.
-   You can remove only worker nodes by calling this operation.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DeleteClusterNodes&type=ROA&version=2015-12-15)

## Request syntax

```
POST /clusters/ClusterId/nodes HTTP/1.1
Content-Type:application/json

{
  "drain_node" : Boolean,
  "release_node" : Boolean,
  "nodes" : [ "String" ]
}
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|c850429a2287b4d968e27e87a4921\*\*\*\*|The ID of the ACK cluster. |

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|drain\_node|Boolean|No|true|Specifies whether to remove all pods from the nodes that you want to remove. Valid values:

 -   `true`: removes all pods from the nodes that you want to remove.
-   `false`: does not remove pods from the nodes that you want to remove.

 Default value: `false`. |
|release\_node|Boolean|No|true|Specifies whether to release the Elastic Compute Service \(ECS\) instances. Valid values:

 -   `true`: releases the ECS instances.
-   `false`: does not release the ECS instances.

 Default value: `false`.

 **Note:** You cannot release subscription ECS instances when you remove the nodes. |
|nodes|Array of String|No|cn-chengdu.192.168.0.70|The names of the nodes that you want to remove. |

## Response syntax

```
HTTP/1.1 200 OK
```

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|cluster\_id|String|c104d5d5f301c4e2a8ee578c37bc\*\*\*\*|The ID of the ACK cluster. |
|request\_id|String|A9891419-D125-4D89-AFCA-68846675E2F7|The ID of the request. |
|task\_id|String|T-60fea8ad2e277f0879000ae9|The ID of the task. |

## Examples

Sample requests

```
POST /clusters/c850429a2287b4d968e27e87a4921****/nodes HTTP/1.1 
Content-Type:application/json
{
  "drain_node" : true,
  "release_node" : true,
  "nodes" : [ "cn-chengdu.192.168.0.70" ]
}
```

Sample success responses

`XML` format

```
HTTP/1.1 200 OK
Content-Type:application/xml

<DeleteClusterNodesResponse>
    <cluster_id>c104d5d5f301c4e2a8ee578c37bc****</cluster_id>
    <request_id>A9891419-D125-4D89-AFCA-68846675E2F7</request_id>
    <task_id>T-60fea8ad2e277f0879000ae9</task_id>
</DeleteClusterNodesResponse>
```

`JSON` format

```
HTTP/1.1 200 OK
Content-Type:application/json

{
  "cluster_id" : "c104d5d5f301c4e2a8ee578c37bc****",
  "request_id" : "A9891419-D125-4D89-AFCA-68846675E2F7",
  "task_id" : "T-60fea8ad2e277f0879000ae9"
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

