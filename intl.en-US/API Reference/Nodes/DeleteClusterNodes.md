# DeleteClusterNodes

Removes nodes from a specified Container Service for Kubernetes \(ACK\) cluster.

**Note:**

-   When you remove a node, the pods that run on the node are migrated to other nodes. This may cause service interruption. We recommend that you remove nodes during off-peak hours.
-   Unknown errors may occur when you remove nodes. Before you remove nodes, we recommend that you back up data of these nodes.
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

None

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

`JSON` format

```
HTTP/1.1 200 OK
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

