# Delete a cluster

You can call the DeleteCluster operation to delete a Container Service for Kubernetes \(ACK\) cluster by ID and release all nodes in the cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DeleteCluster&type=ROA&version=2015-12-15)

## Request syntax

```
DELETE /clusters/ClusterId?retain_all_resources=Boolean&keep_slb=Boolean&retain_resources=["String"] HTTP/1.1
Content-Type:application/json
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|c905d1364c2dd4b6284a3f41790c4\*\*\*\*|The ID of the cluster. |

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|retain\_all\_resources|Boolean|No|false|Specifies whether to retain all resources. If you set the parameter to `true`, the `retain_resources` parameter is ignored.

 -   `true`: retains all resources.
-   `false`: does not retain all resources.

 Default value: `false`. |
|keep\_slb|Boolean|No|false|Specifies whether to retain the Server Load Balancer \(SLB\) instances that are created by the cluster.

 -   `true`: retains the SLB instances that are created by the cluster.
-   `false`: does not retain the SLB instances that are created by the cluster.

 Default value: `false`. |
|retain\_resources|Array of String|No|sg-2ze6ess9kho6fdn9\*\*\*\*|The names of the resources that you want to retain. |

## Response syntax

```
HTTP/1.1 200 OK
```

## Response parameters

None

## Examples

Sample requests

```
DELETE /clusters/c905d1364c2dd4b6284a3f41790c4****?retain_all_resources=false&keep_slb=false&retain_resources=["sg-2ze6ess9kho6fdn9****"] HTTP/1.1
Host:cs.aliyuncs.com
Content-Type:application/json
```

Sample success responses

`JSON` format

```
HTTP/1.1 200 OK
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

