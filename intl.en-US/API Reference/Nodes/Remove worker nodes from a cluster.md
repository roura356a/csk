# Remove worker nodes from a cluster

You can call DeleteClusterNodes to remove worker nodes from a cluster.

**Notes**

-   When you remove a node, the pods running on this node are migrated to other nodes, which may cause service interruptions. We recommend that you remove nodes during off-peak hours.
-   Unexpected errors may occur when you remove nodes. We recommend that you back up your data in advance.
-   A node that is being removed cannot be scheduled.
-   You can remove worker nodes only.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DeleteClusterNodes&type=ROA&version=2015-12-15)

## Request headers

This operation uses the common request header only. For more information, see Common parameters.

## Request syntax

```
POST|DELETE /clusters/[ClusterId]/nodes HTTPS|HTTP
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|c106f377e16f34eb1808d6b9362c9\*\*\*\*|The ID of the cluster. |
|nodes|List<String\>|Yes|\["i-0xi187lghfcy5t2d\*\*\*\*","i-0xi4s3kfic7ih8vs\*\*\*\*"\]|A list of the nodes that you want to remove. |
|release\_node|String|No|false|Specifies whether to release the Elastic Compute Service \(ECS\) instances when they are removed from the cluster. By default, the ECS instances are not released. |

\{

"nodes":"A list of the nodes that you want to remove.",

"release\_node":"Specifies whether to release the ECS instances when they are removed from the cluster."

\}

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|RequestId|String|bdb7ad99-5fcf-450b-9db0-0eeb6de31d1b|The ID of the request. |

HTTP/1.1 202 OK

## Examples

Sample requests

```
POST /clusters/[ClusterId]/nodes HTTP/1.1
Common request header
{
    "nodes": [
        "i-xxxx",
        "i-yyyy"
    ],
    "release_node": false
}
```

Sample success responses

`XML` format

```
<RequestId>bdb7ad99-5fcf-450b-9db0-0eeb6de31d1b</RequestId>
```

`JSON` format

```
{"RequestId":"bdb7ad99-5fcf-450b-9db0-0eeb6de31d1b"}
```

Sample response description

```
HTTP/1.1 202 Accepted
<Common response header>
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

