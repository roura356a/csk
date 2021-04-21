# ModifyCluster

Modifies the configurations of a specified Container Service for Kubernetes \(ACK\) cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=ModifyCluster&type=ROA&version=2015-12-15)

## Request syntax

```
PUT /api/v2/clusters/ClusterId HTTP/1.1 
Content-Type:application/json
{
  "api_server_eip" : Boolean,
  "api_server_eip_id" : "String",
  "deletion_protection" : Boolean,
  "instance_deletion_protection" : Boolean,
  "ingress_domain_rebinding" : "String",
  "ingress_loadbalancer_id" : "String",
  "resource_group_id" : "String",
  "maintenance_window" : {
    "enable" : Boolean,
    "maintenance_time" : "String",
    "duration" : "String",
    "weekly_period" : "String"
  }
}
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|cb95aa626a47740afbf6aa099b65\*\*\*\*|The ID of the ACK cluster. |

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|api\_server\_eip|Boolean|No|true|Specifies whether to associate an elastic IP address \(EIP\) with the cluster API server. This enables Internet access for the cluster. Valid values:

 -   `true`: associates an EIP with the cluster API server.
-   `false`: does not associate an EIP with the cluster API server. |
|api\_server\_eip\_id|String|No|eip-wz9fnasl6dsfhmvci\*\*\*\*|The ID of the EIP that you want to associate with the cluster API server. The parameter takes effect only if `api_server_eip` is set to `true`. |
|deletion\_protection|Boolean|No|true|Specifies whether to enable deletion protection for the cluster. If deletion protection is enabled, the cluster cannot be deleted in the console or by calling the API. Valid values:

 -   `true`: enables deletion protection for the cluster. You cannot delete the cluster in the console or by calling the API.
-   `false`: does not enable deletion protection for the cluster. You can delete the cluster in the console or by calling the API

 Default value: `false`. |
|instance\_deletion\_protection|Boolean|No|true|Specifies whether to enable deletion protection for the instances in the cluster. If deletion protection is enabled, the instances in the cluster cannot be deleted in the console or by calling the API. Valid values:

 -   `true`: enables deletion protection for the instances in the cluster. You cannot delete the instances in the cluster in the console or by calling the API.
-   `false`: does not enable deletion protection for the instances in the cluster. You can delete the instances in the cluster in the console or by calling the API

 Default value: `false`. |
|ingress\_domain\_rebinding|String|No|true|Specifies whether to rebind the test domain name of the cluster. Valid values:

 -   `true`: rebinds the test domain name of the cluster.
-   `false`: does not rebind the test domain name of the cluster.

 Default value: `false`. |
|ingress\_loadbalancer\_id|String|No|lb-wz97kes8tnndkpodw\*\*\*\*|Specifies whether to change the ID of the Server Load Balancer \(SLB\) instance that is associated with the cluster. |
|resource\_group\_id|String|No|rg-acfmyvw3wjm\*\*\*\*|The ID of the resource group to which the cluster belongs. |
|maintenance\_window|[maintenance\_window](/intl.en-US/API Reference/Commonly used parameters.md)|No| |The details of the maintenance window of the cluster. This parameter takes effect in only professional Kubernetes clusters. |

## Response syntax

```
HTTP/1.1 200
Content-Type:application/json
{
  "cluster_id" : "String",
  "request_id" : "String",
  "task_id" : "String"
}
```

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|cluster\_id|String|cb95aa626a47740afbf6aa09\*\*\*\*|The ID of the ACK cluster. |
|request\_id|String|687C5BAA-D103-4993-884B-C35E4314\*\*\*\*|The ID of the request. |
|task\_id|String|T-5a54309c80282e39ea00\*\*\*\*|The ID of the task. |

## Examples

Sample requests

```
PUT /api/v2/clusters/cb95aa626a47740afbf6aa099b65**** HTTP/1.1 
Content-Type:application/json
{
  "api_server_eip" : true,
  "api_server_eip_id" : "eip-wz9fnasl6dsfhmvci****",
  "deletion_protection" : true,
  "instance_deletion_protection" : true,
  "ingress_domain_rebinding" : "true",
  "ingress_loadbalancer_id" : "lb-wz97kes8tnndkpodw****",
  "resource_group_id" : "rg-acfmyvw3wjm****",
  "maintenance_window" : {
    "enable" : false,
    "maintenance_time" : "03:00:00Z",
    "duration" : "3h",
    "weekly_period" : "Monday,Thursday"
  }
}
```

Sample success responses

`XML` format

```
HTTP/1.1 200 OK
Content-Type:application/xml

<cluster_id>cb95aa626a47740afbf6aa09****</cluster_id>
<request_id>687C5BAA-D103-4993-884B-C35E4314****</request_id>
<task_id>T-5a54309c80282e39ea00****</task_id>
```

`JSON` format

```
HTTP/1.1 200 OK
Content-Type:application/json

{
  "cluster_id" : "cb95aa626a47740afbf6aa09****",
  "request_id" : "687C5BAA-D103-4993-884B-C35E4314****",
  "task_id" : "T-5a54309c80282e39ea00****"
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

