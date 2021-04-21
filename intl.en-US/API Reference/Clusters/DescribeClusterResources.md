# DescribeClusterResources

Queries all resources in a specified Container Service for Kubernetes \(ACK\) cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DescribeClusterResources&type=ROA&version=2015-12-15)

## Request syntax

```
GET /clusters/ClusterId/resources HTTP/1.1 
Content-Type:application/json
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|cb95aa626a47740afbf6aa099b65\*\*\*\*|The ID of the ACK cluster. |

## Response syntax

```
HTTP/1.1 200
Content-Type:application/json
[ {
  "cluster_id" : "String",
  "created" : "String",
  "instance_id" : "String",
  "resource_info" : "String",
  "resource_type" : "String",
  "state" : "String"
} ]
```

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
| |Array| |The list of the resources in the ACK cluster. |
|cluster\_id|String|cb95aa626a47740afbf6aa099b65\*\*\*\*|The ID of the ACK cluster. |
|created|String|2020-09-11T10:11:54+08:00|The time when the resource was created. |
|instance\_id|String|lb-wz9poz4r0ymh8u0uf\*\*\*\*|The ID of the resource. |
|resource\_info|String|\{\\"Id\\":\\"k8s\_master\_slb\\",\\"Name\\":\\"k8s\_master\_slb\\",\\"Type\\":\\"ALIYUN::SLB::LoadBalancer\\",\\"Status\\":\\"CREATE\_COMPLETE\\",\\"StatusReason\\":\\"state changed\\",\\"Updated\\":\\"2020-05-21T13:25:02\\",\\"PhysicalId\\":\\"lb-wz9poz4r0ymh8u0uf\*\*\*\*\\"\}|The information about the resource. For more information about how to query the source information, see [ListStackResources](~~133836~~). |
|resource\_type|String|ALIYUN::SLB::LoadBalancer|The type of resource. |
|state|String|CREATE\_COMPLETE|The state of the resource. Valid values:

 -   `CREATE_COMPLETE`: The resource is successfully created.
-   `CREATE_FAILED`: The resource failed to be created.
-   `CREATE_IN_PROGRESS`: The resource is being created.
-   `DELETE_FAILED`: The resource failed to be deleted.
-   `DELETE_IN_PROGRESS`: The resource is being deleted.
-   `ROLLBACK_COMPLETE`: The resource is successfully rolled back.
-   `ROLLBACK_FAILED`: The resource failed to be rolled back.
-   `ROLLBACK_IN_PROGRESS`: The resource is being rolled back. |

## Examples

Sample requests

```
GET /clusters/cb95aa626a47740afbf6aa099b65****/resources HTTP/1.1 
Content-Type:application/json
```

Sample success responses

`XML` format

```
HTTP/1.1 200 OK
Content-Type:application/xml

<0>
    <cluster_id>cb95aa626a47740afbf6aa099b65****</cluster_id>
    <created>2020-09-11T10:11:54+08:00</created>
    <instance_id>lb-wz9poz4r0ymh8u0uf****</instance_id>
    <resource_info>{\"Id\":\"k8s_master_slb\",\"Name\":\"k8s_master_slb\",\"Type\":\"ALIYUN::SLB::LoadBalancer\",\"Status\":\"CREATE_COMPLETE\",\"StatusReason\":\"state changed\",\"Updated\":\"2020-05-21T13:25:02\",\"PhysicalId\":\"lb-wz9poz4r0ymh8u0uf****\"}</resource_info>
    <resource_type>ALIYUN::SLB::LoadBalancer</resource_type>
    <state>CREATE_COMPLETE</state>
</0>
```

`JSON` format

```
HTTP/1.1 200 OK
Content-Type:application/json

[ {
  "cluster_id" : "cb95aa626a47740afbf6aa099b65****",
  "created" : "2020-09-11T10:11:54+08:00",
  "instance_id" : "lb-wz9poz4r0ymh8u0uf****",
  "resource_info" : "{\\\"Id\\\":\\\"k8s_master_slb\\\",\\\"Name\\\":\\\"k8s_master_slb\\\",\\\"Type\\\":\\\"ALIYUN::SLB::LoadBalancer\\\",\\\"Status\\\":\\\"CREATE_COMPLETE\\\",\\\"StatusReason\\\":\\\"state changed\\\",\\\"Updated\\\":\\\"2020-05-21T13:25:02\\\",\\\"PhysicalId\\\":\\\"lb-wz9poz4r0ymh8u0uf****\\\"}",
  "resource_type" : "ALIYUN::SLB::LoadBalancer",
  "state" : "CREATE_COMPLETE"
} ]
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

