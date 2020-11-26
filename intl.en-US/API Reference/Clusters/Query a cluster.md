# Query a cluster

You can call DescribeClusterDetail to query the details of a cluster based on the cluster ID.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DescribeClusterDetail&type=ROA&version=2015-12-15)

## Request parameter types

This operation uses only common request parameters. For more information, see Common parameters.

## Request syntax

```
get /clusters/{ClusterId} http|https
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|cdde1f21ae22e483ebcb068a6eb7f\*\*\*\*|The ID of the cluster. |

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|cluster\_id|String|"c82e6987e2961451182edacd74faf\*\*\*\*"|The ID of the cluster. |
|cluster\_type|String|"Kubernetes"|The type of the cluster. |
|created|String|"2019-11-25T15:50:20+08:00"|The time when the cluster was created. |
|current\_version|String|"1.14.8-aliyun.1"|The version of the cluster. |
|deletion\_protection|Boolean|"true"|Indicates whether deletion protection is enabled for the cluster. If this feature is enabled, the cluster cannot be deleted by operations in the console or API operations. |
|docker\_version|String|"19.03.5"|The version of Docker. |
|external\_loadbalancer\_id|String|"lb-2ze3buguz3gx9920z\*\*\*\*"|The ID of the Server Load Balancer \(SLB\) instance deployed in the cluster. |
|instance\_type|String|"\*\*\*"|The Elastic Compute Service \(ECS\) instance type of cluster nodes. |
|meta\_data|String|"\*\*\*"|The metadata of the cluster. |
|name|String|"cluster-demo"|The name of the cluster. |
|network\_mode|String|"vpc"|The network type of the cluster. The value vpc indicates that the cluster uses a virtual private cloud \(VPC\). |
|region\_id|String|"cn-beijing"|The ID of the region where the cluster is deployed. |
|resource\_group\_id|String|"rg-acfmyvw3wjm\*\*\*\*"|The ID of the resource group to which the cluster belongs. |
|security\_group\_id|String|"sg-25yq\*\*\*\*"|The ID of the security group to which the cluster belongs. |
|size|Integer|"6"|The number of nodes in the cluster. |
|state|String|"running"|The status of the cluster. Valid values:

 -   running: The cluster is running.
-   stopped: The cluster is stopped.
-   deleted: The cluster is deleted.
-   delete\_failed: Failed to delete the cluster.
-   failed: Failed to create the cluster.
-   scaling: The cluster is being scaled out. |
|tags|Array of tags| |The tags of the cluster. |
|key|String|"tier"|The key of the tag. |
|value|String|"frontend"|The value of the tag. |
|updated|String|"2020-01-13T23:01:03+08:00"|The time when the cluster was updated. |
|vpc\_id|String|"vpc-2zecuu62b9zw7a7qn\*\*\*\*"|The ID of the VPC used by the cluster. |
|vswitch\_cidr|String|"26"|The CIDR block of VSwitches. |
|vswitch\_id|String|"vsw-2zete8s4qocqg0mf6\*\*\*\*,vsw-2zete8s4qocqg0mf6\*\*\*\*"|The IDs of VSwitches. |
|zone\_id|String|"cn-beijing-a"|The ID of the zone where the cluster is deployed. |

## Examples

Sample requests

```
GET /clusters/[ClusterId] HTTP/1.1
Common request parameters
{
    "ClusterId":"cdde1f21ae22e483ebcb068a6eb7f****"
}
```

Sample success responses

`XML` format

```
<resource_group_id>rg-acfmyvw3wjm****</resource_group_id>
<vpc_id>vpc-2zecuu62b9zw7a7qn****</vpc_id>
<created>2019-11-25T15:50:20+08:00</created>
<deletion_protection>true</deletion_protection>
<network_mode>vpc</network_mode>
<region_id>cn-beijing</region_id>
<current_version>1.14.8-aliyun.1</current_version>
<security_group_id>sg-25yq****</security_group_id>
<cluster_type>Kubernetes</cluster_type>
<docker_version>19.03.5</docker_version>
<tags>
    <value>frontend</value>
    <key>tier</key>
</tags>
<vswitch_cidr>26</vswitch_cidr>
<zone_id>cn-beijing-a</zone_id>
<cluster_id>c82e6987e2961451182edacd74faf****</cluster_id>
<size>6</size>
<external_loadbalancer_id>lb-2ze3buguz3gx9920z****</external_loadbalancer_id>
<meta_data>***</meta_data>
<name>cluster-demo</name>
<vswitch_id>vsw-2zete8s4qocqg0mf6****,vsw-2zete8s4qocqg0mf6****</vswitch_id>
<state>running</state>
<instance_type>***</instance_type>
<updated>2020-01-13T23:01:03+08:00</updated>
```

`JSON` format

```
{
    "resource_group_id": "rg-acfmyvw3wjm****",
    "vpc_id": "vpc-2zecuu62b9zw7a7qn****",
    "created": "2019-11-25T15:50:20+08:00",
    "deletion_protection": "true",
    "network_mode": "vpc",
    "region_id": "cn-beijing",
    "current_version": "1.14.8-aliyun.1",
    "security_group_id": "sg-25yq****",
    "cluster_type": "Kubernetes",
    "docker_version": "19.03.5",
    "tags": [
        {
            "value": "frontend",
            "key": "tier"
        }
    ],
    "vswitch_cidr": "26",
    "zone_id": "cn-beijing-a",
    "cluster_id": "c82e6987e2961451182edacd74faf****",
    "size": "6",
    "external_loadbalancer_id": "lb-2ze3buguz3gx9920z****",
    "meta_data": "***",
    "name": "cluster-demo",
    "vswitch_id": "vsw-2zete8s4qocqg0mf6****,vsw-2zete8s4qocqg0mf6****",
    "state": "running",
    "instance_type": "***",
    "updated": "2020-01-13T23:01:03+08:00"
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

