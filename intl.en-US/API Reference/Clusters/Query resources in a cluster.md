# Query resources in a cluster

You can call DescribeClusterResources to query all resources in a cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DescribeClusterResources&type=ROA&version=2015-12-15)

## Request parameter types

This operation uses only common request parameters. For more information, see Common parameters.

## Request syntax

```
get /clusters/{ClusterId}/resources http|https
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|cb95aa626a47740afbf6aa099b65\*\*\*\*|The ID of the cluster. |

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
| |Array| |A list of resources in the cluster. |
|cluster\_id|String|"cb95aa626a47740afbf6aa099b65\*\*\*\*"|The ID of the cluster. |
|created|String|"2020-09-11T10:11:54+08:00"|The time when the resource was created. |
|instance\_id|String|"lb-wz9poz4r0ymh8u0uf\*\*\*\*"|The ID of the resource. |
|resource\_info|String|"\{\\\\\\"Id\\\\\\":\\\\\\"k8s\_master\_slb\\\\\\",\\\\\\"Name\\\\\\":\\\\\\"k8s\_master\_slb\\\\\\",\\\\\\"Type\\\\\\":\\\\\\"ALIYUN::SLB::LoadBalancer\\\\\\",\\\\\\"Status\\\\\\":\\\\\\"CREATE\_COMPLETE\\\\\\",\\\\\\"StatusReason\\\\\\":\\\\\\"state changed\\\\\\",\\\\\\"Updated\\\\\\":\\\\\\"2020-05-21T13:25:02\\\\\\",\\\\\\"PhysicalId\\\\\\":\\\\\\"lb-wz9poz4r0ymh8u0uf\*\*\*\*\\\\\\"\}"|The details of the resource. For more information, see [Resource details](～～28916～～). |
|resource\_type|String|"ALIYUN::SLB::LoadBalancer"|The type of the resource. |
|state|String|"CREATE\_COMPLETE"|The status of the resource. Valid values: CREATE\_COMPLETE, CREATE\_FAILED, CREATE\_IN\_PROGRESS, DELETE\_FAILED, DELETE\_IN\_PROGRESS, ROLLBACK\_COMPLETE, ROLLBACK\_FAILED, and ROLLBACK\_IN\_PROGRESS. |

## Examples

Sample requests

```
GET /clusters/[ClusterId]/resources HTTP/1.1
Common request parameters
{
    "ClusterId":"cb95aa626a47740afbf6aa099b65****"
}
```

Sample success responses

`XML` format

```
<0>
    <instance_id>i-wz9aixnfbjo1txoq****</instance_id>
    <resource_type>ALIYUN::ECS::InstanceGroup</resource_type>
    <resource_info>{"Id":"k8s_master_2","Name":"k8s_master_2","Type":"ALIYUN::ECS::InstanceGroup","Status":"CREATE_COMPLETE","StatusReason":"state changed","Updated":"2020-05-21T13:26:04","PhysicalId":"i-wz9aixnfbjo1txoq****"}</resource_info>
    <created>2020-05-21T21:39:19+08:00</created>
    <state>CREATE_COMPLETE</state>
</0>
<1>
    <instance_id>i-wz976s1is8lo28ue****</instance_id>
    <resource_type>ALIYUN::ECS::InstanceGroup</resource_type>
    <resource_info>{"Id":"k8s_master_3","Name":"k8s_master_3","Type":"ALIYUN::ECS::InstanceGroup","Status":"CREATE_COMPLETE","StatusReason":"state changed","Updated":"2020-05-21T13:26:43","PhysicalId":"i-wz976s1is8lo28ue****"}</resource_info>
    <created>2020-05-21T21:39:19+08:00</created>
    <state>CREATE_COMPLETE</state>
</1>
```

`JSON` format

```
[
  {
    "instance_id": "i-wz9aixnfbjo1txoq****",
    "resource_type": "ALIYUN::ECS::InstanceGroup",
    "resource_info": "{\"Id\":\"k8s_master_2\",\"Name\":\"k8s_master_2\",\"Type\":\"ALIYUN::ECS::InstanceGroup\",\"Status\":\"CREATE_COMPLETE\",\"StatusReason\":\"state changed\",\"Updated\":\"2020-05-21T13:26:04\",\"PhysicalId\":\"i-wz9aixnfbjo1txoq****\"}",
    "created": "2020-05-21T21:39:19+08:00",
    "state": "CREATE_COMPLETE"
  },
  {
    "instance_id": "i-wz976s1is8lo28ue****",
    "resource_type": "ALIYUN::ECS::InstanceGroup",
    "resource_info": "{\"Id\":\"k8s_master_3\",\"Name\":\"k8s_master_3\",\"Type\":\"ALIYUN::ECS::InstanceGroup\",\"Status\":\"CREATE_COMPLETE\",\"StatusReason\":\"state changed\",\"Updated\":\"2020-05-21T13:26:43\",\"PhysicalId\":\"i-wz976s1is8lo28ue****\"}",
    "created": "2020-05-21T21:39:19+08:00",
    "state": "CREATE_COMPLETE"
  }
]
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

