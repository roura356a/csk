# Modify a cluster

You can call ModifyCluster to modify a cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=ModifyCluster&type=ROA&version=2015-12-15)

## Request parameter types

This operation uses only common request parameters. For more information, see Common parameters.

## Request syntax

```
put /api/v2/clusters/{ClusterId} http|https
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|cb95aa626a47740afbf6aa099b65\*\*\*\*|The ID of the cluster. |
|deletion\_protection|Boolean|No|true|Specifies whether to enable deletion protection for the cluster. |
|ingress\_loadbalancer\_id|String|No|lb-wz97kes8tnndkpodw\*\*\*\*|The ID of the Server Load Balancer \(SLB\) instance associated with the ingress of the cluster. |
|api\_server\_eip|Boolean|No|true|Specifies whether to assign an elastic IP address \(EIP\) to the API server of the cluster. |
|api\_server\_eip\_id|String|No|eip-wz9fnasl6dsfhmvci\*\*\*\*|The ID of the EIP that is assigned to the API server of the cluster. |
|resource\_group\_id|String|No|""|The ID of the resource group to which the cluster belongs. |
|ingress\_domain\_rebinding|String|No|true|Specifies whether to rebind the default domain name of the cluster to the public IP address of the SLB instance associated with the ingress of the cluster. |

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|cluster\_id|String|"cb95aa626a47740afbf6aa09\*\*\*\*"|The ID of the cluster. |
|request\_id|String|"687C5BAA-D103-4993-884B-C35E4314\*\*\*\*"|The ID of the request. |
|task\_id|String|"T-5a54309c80282e39ea00\*\*\*\*"|The ID of the task. |

## Examples

Sample requests

```
PUT /api/v2/clusters/[ClusterId] HTTP/1.1
Common request parameters
{
    "api_server_eip":"true",
    "api_server_eip_id":"eip-wz9fnasl6dsfhmvci****",
    "ClusterId":"cb95aa626a47740afbf6aa099b65****",
    "deletion_protection":"true",
    "ingress_loadbalancer_id":"lb-wz97kes8tnndkpodw****",
    "resource_group_id":""
}
```

Sample success responses

`XML` format

```
<cluster_id>cb95aa626a47740afbf6aa09****</cluster_id>
<task_id>T-5a54309c80282e39ea00****</task_id>
<request_id>687C5BAA-D103-4993-884B-C35E4314****</request_id>
```

`JSON` format

```
{
    "cluster_id": "cb95aa626a47740afbf6aa09****",
    "task_id": "T-5a54309c80282e39ea00****",
    "request_id": "687C5BAA-D103-4993-884B-C35E4314****"
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

