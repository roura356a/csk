# Query the information about all clusters

You can call DescribeClustersV1 to query the information about all clusters created in the Alibaba Cloud Container Service for Kubernetes \(ACK\) console.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DescribeClustersV1&type=ROA&version=2015-12-15)

## Request parameter types

This operation uses common request parameters only. For more information, see Common parameters.

## Request syntax

```
get /api/v1/clusters http|https
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|Name|String|No|cluster-demo|The name of the cluster. |
|ClusterType|String|No|Kubernetes|The type of the cluster. |
|page\_size|Long|No|10|The number of entries to return on each page. |
|page\_number|Long|No|3|The number of the page to return. |

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|clusters|Array of clusters| |A list of clusters. |
|cluster\_healthy|String|"\*\*\*"|The health status of the cluster. |
|cluster\_id|String|"c3fb96524f9274b4495df0f12a6b5\*\*\*\*"|The ID of the cluster. |
|cluster\_type|String|"Kubernetes"|The type of the cluster. Valid values:

-   Kubernetes: a dedicated cluster.
-   ManagedKubernetes: a standard managed cluster or a managed edge cluster.
-   Ask: a standard serverless cluster. |
|created|String|"2020-08-20T10:51:29+08:00"|The time when the cluster was created. |
|current\_version|String|"1.16.9-aliyun.1"|The version of the cluster. |
|data\_disk\_category|String|"cloud"|The type of data disks. |
|data\_disk\_size|Long|"120"|The size of a data disk. |
|deletion\_protection|Boolean|"true"|Indicates whether deletion protection is enabled for the cluster. If this feature is enabled, the cluster cannot be deleted by operations in the console or API operations. |
|docker\_version|String|"19.03.5"|The version of Docker. |
|external\_loadbalancer\_id|String|"lb-2vcrbmlevo6kjpgch\*\*\*\*"|The ID of the SLB instance that is associated with the ingress of the cluster. |
|init\_version|String|"1.16.9-aliyun.1"|The initial version of the cluster. |
|master\_url|String|"\*\*\*"|The endpoints that are open for connections to the cluster. The endpoints include api\_server\_endpoint and dashboard\_endpoint. |
|meta\_data|String|"\*\*\*"|The metadata of the cluster. |
|name|String|"cluster-demo"|The name of the cluster. |
|network\_mode|String|"vpc"|The network type of the cluster. Valid values: vpc and ipvs. |
|node\_status|String|"running"|The status of cluster nodes. |
|private\_zone|Boolean|"false"|Indicates whether PrivateZone is enabled for the cluster. |
|profile|String|"Default"|The identifier of the cluster. Valid values:

-   Edge: a managed edge cluster.
-   Default: a cluster of another type instead of a managed edge cluster. |
|region\_id|String|"cn-beijing"|The ID of the region where the cluster is deployed. |
|resource\_group\_id|String|"rg-acfmyvw3wjm\*\*\*\*"|The ID of the resource group to which the cluster belongs. |
|security\_group\_id|String|"sg-2vcgwsrwgt5mp0yi\*\*\*\*"|The ID of the security group to which the cluster belongs. |
|size|Long|"6"|The number of cluster nodes. |
|state|String|"running"|The status of the cluster. |
|subnet\_cidr|String|"172.21.0.0/16"|The CIDR block of pods in the cluster. |
|tags|Array of tags| |The tags of the cluster. |
|key|String|"tier"|The key of the tag. |
|value|String|"frontend"|The value of the tag. |
|updated|String|"2020-09-16T11:09:55+08:00"|The time when the cluster was updated. |
|vpc\_id|String|"vpc-2vcg932hsxsxuqbgl\*\*\*\*"|The ID of the virtual private cloud \(VPC\) used by the cluster. |
|vswitch\_cidr|String|"\*\*\*"|The CIDR block of VSwitches. |
|vswitch\_id|String|"vsw-2vc41xuumx5z2rdma\*\*\*\*,vsw-2vc41xuumx5z2rdma\*\*\*\*"|The IDs of VSwitches. |
|worker\_ram\_role\_name|String|"\*\*\*"|The name of the RAM role for worker nodes in the cluster. |
|zone\_id|String|"cn-beijing-b"|The ID of the zone to which the cluster belongs. |
|page\_info|Object| |The pagination details. |
|page\_number|Integer|"3"|The page number of the returned page. |
|page\_size|Integer|"20"|The number of entries returned per page. |
|total\_count|Integer|"50"|The total number of entries returned. |

## Examples

Sample requests

```
GET /api/v1/clusters HTTP/1.1
Common request parameters
```

Sample success responses

`XML` format

```
<page_info>
    <page_number>3</page_number>
    <total_count>50</total_count>
    <page_size>20</page_size>
</page_info>
<clusters>
    <private_zone>false</private_zone>
    <resource_group_id>rg-acfmyvw3wjm****</resource_group_id>
    <vpc_id>vpc-2vcg932hsxsxuqbgl****</vpc_id>
    <network_mode>vpc</network_mode>
    <security_group_id>sg-2vcgwsrwgt5mp0yi****</security_group_id>
    <cluster_type>Kubernetes</cluster_type>
    <docker_version>19.03.5</docker_version>
    <data_disk_category>cloud</data_disk_category>
    <zone_id>cn-beijing-b</zone_id>
    <cluster_id>c3fb96524f9274b4495df0f12a6b5****</cluster_id>
    <external_loadbalancer_id>lb-2vcrbmlevo6kjpgch****</external_loadbalancer_id>
    <meta_data>***</meta_data>
    <vswitch_id>vsw-2vc41xuumx5z2rdma****,vsw-2vc41xuumx5z2rdma****</vswitch_id>
    <state>running</state>
    <init_version>1.16.9-aliyun.1</init_version>
    <node_status>running</node_status>
    <created>2020-08-20T10:51:29+08:00</created>
    <deletion_protection>true</deletion_protection>
    <profile>Default</profile>
    <subnet_cidr>172.21.0.0/16</subnet_cidr>
    <master_url>***</master_url>
    <region_id>cn-beijing</region_id>
    <current_version>1.16.9-aliyun.1</current_version>
    <tags>
        <value>frontend</value>
        <key>tier</key>
    </tags>
    <vswitch_cidr>***</vswitch_cidr>
    <cluster_healthy>***</cluster_healthy>
    <data_disk_size>120</data_disk_size>
    <size>6</size>
    <name>cluster-demo</name>
    <updated>2020-09-16T11:09:55+08:00</updated>
    <worker_ram_role_name>***</worker_ram_role_name>
</clusters>
```

`JSON` format

```
{
    "page_info": {
        "page_number": "3",
        "total_count": "50",
        "page_size": "20"
    },
    "clusters": {
        "private_zone": "false",
        "resource_group_id": "rg-acfmyvw3wjm****",
        "vpc_id": "vpc-2vcg932hsxsxuqbgl****",
        "network_mode": "vpc",
        "security_group_id": "sg-2vcgwsrwgt5mp0yi****",
        "cluster_type": "Kubernetes",
        "docker_version": "19.03.5",
        "data_disk_category": "cloud",
        "zone_id": "cn-beijing-b",
        "cluster_id": "c3fb96524f9274b4495df0f12a6b5****",
        "external_loadbalancer_id": "lb-2vcrbmlevo6kjpgch****",
        "meta_data": "***",
        "vswitch_id": "vsw-2vc41xuumx5z2rdma****,vsw-2vc41xuumx5z2rdma****",
        "state": "running",
        "init_version": "1.16.9-aliyun.1",
        "node_status": "running",
        "created": "2020-08-20T10:51:29+08:00",
        "deletion_protection": "true",
        "profile": "Default",
        "subnet_cidr": "172.21.0.0/16",
        "master_url": "***",
        "region_id": "cn-beijing",
        "current_version": "1.16.9-aliyun.1",
        "tags": {
            "value": "frontend",
            "key": "tier"
        },
        "vswitch_cidr": "***",
        "cluster_healthy": "***",
        "data_disk_size": "120",
        "size": "6",
        "name": "cluster-demo",
        "updated": "2020-09-16T11:09:55+08:00",
        "worker_ram_role_name": "***"
    }
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

