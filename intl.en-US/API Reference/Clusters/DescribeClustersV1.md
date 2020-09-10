# DescribeClustersV1

You can call DescribeClustersV1 to query the information about all clusters created in the Alibaba Cloud Container Service for Kubernetes \(ACK\) console \(including Swarm clusters\).

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. You can use OpenAPI Explorer to search for API operations, call API operations, and dynamically generate SDK sample codes.](https://api.aliyun.com/#product=CS&api=DescribeClustersV1&type=ROA&version=2015-12-15)

## Request headers

This operation uses only common request headers. For more information, see the Common request parameters topic.

## Request syntax

```
GET /api/v1/clusters HTTPS|HTTP
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|Name|String|No|test|A keyword of the cluster name. The clusters whose names contain the keyword are returned. |
|ClusterType|String|No|ManagedKubernetes|The type of the cluster. Clusters of the specified types are returned.

-   Kubernetes: the cluster of ACK Proprietary Edition.
-   ManagedKubernetes: the ACK Standard cluster and the cluster of ACK Edge Edition.
-   Ask: the serverless Kubernetes \(ASK\) cluster. |

## Response

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|RequestId|String|348D4C9C-9105-4A1B-A86E-B58F0F875575|The ID of the request. |
|clusters|Array of clusters| |Information about all clusters that match the request. |
|clusters| | | |
|cluster\_healthy|String|""|The health status of the cluster. |
|cluster\_id|String|c6e31bbe4dd70499b8ff51aac0586\*\*\*\*|The ID of the cluster. |
|cluster\_spec|String|""|The specifications of master nodes in clusters of ACK Managed Edition. |
|cluster\_type|String|Kubernetes|The type of the cluster. Valid values:

-   Kubernetes: the cluster of ACK Proprietary Edition.
-   ManagedKubernetes: the ACK Standard cluster and ACK Pro cluster.
-   Ask: the serverless Kubernetes \(ASK\) cluster. |
|created|String|2020-08-10T14:41:58+08:00|The time when the cluster was created. |
|current\_version|String|1.16.9-aliyun.1|The version of the cluster. |
|data\_disk\_category|String|cloud|The data disk type of worker nodes. |
|data\_disk\_size|Integer|120|The size of a data disk. Unit: GB. |
|deletion\_protection|Boolean|true|Indicates whether deletion protection is enabled for the cluster. If the value is true, it indicates that the cluster cannot be deleted in the console or by calling API operations. |
|docker\_version|String|19.03.5|The version of Docker. |
|enabled\_migration|Boolean|false|Indicates whether the migration of master nodes is enabled. |
|external\_loadbalancer\_id|String|""|The ID of the SLB instance that is associated with the Ingress of the cluster. |
|gw\_bridge|String|""|The name of the bridge. This parameter is only returned for Swarm clusters. |
|init\_version|String|1.16.9-aliyun.1|The initial version of the cluster. |
|instance\_type|String|""|The Elastic Compute Service \(ECS\) instance type of cluster nodes. |
|master\_url|String|""|The endpoints that are open for connections to the cluster. The endpoints include api\_server\_endpoint, dashboard\_endpoint, mirana\_endpoint, reverse\_tunnel\_endpoint, and intranet\_api\_server\_endpoint. For more information, see [Use SSH to connect to a cluster](～～86491～～). |
|meta\_data|String|""|The metadata of the cluster. |
|name|String|test-cluster|The name of the cluster. |
|need\_update\_agent|Boolean|false|Indicates whether agents deployed in the cluster and parameters of Swarm clusters are updated. |
|network\_mode|String|vpc|Indicates the network mode of the cluster. Valid values: VPC and IPVS. |
|node\_status|String|""|The status of cluster nodes. |
|port|Integer|0|N/A |
|private\_zone|Boolean|false|Indicates whether private zones are enabled for the cluster. |
|profile|String|Default|Then identifier of the cluster. Valid values:

-   Edge: the cluster of ACK Edge Edition.
-   Default: the other ACK clusters. |
|region\_id|String|cn-chengd|The ID of the area where the cluster is deployed. |
|resource\_group\_id|String|rg-acfmyvw3wjm\*\*\*\*|The ID of the resource group to which the cluster belongs. |
|security\_group\_id|String|sg-2vc6as1538wretr1\*\*\*\*|The ID of the security group to which the cluster belongs. |
|size|Integer|3|The number of cluster nodes. Master nodes and worker nodes are included. |
|state|String|running|The status of the cluster. Valid values: |
|subnet\_cidr|String|172.20.0.0/16|The CIDR block of pods in the cluster. |
|swarm\_mode|Boolean|false|Indicates whether the cluster is a Swarm cluster. |
|tags|Array of tags| |The tags of the cluster. |
|tags| | | |
|key|String|ack.aliyun.com| |
|value|String|c6e31bbe4dd70499b8ff51aac0586\*\*\*\*| |
|updated|String|2020-08-14T16:17:38+08:00|The time when the cluster was last upgraded or updated. |
|vpc\_id|String|vpc-2vcg932hsxsxuqbglzhp3|The ID of the VPC network where the cluster is deployed. |
|vswitch\_cidr|String|""|The CIDR block of VSwitches in the VPC network. |
|vswitch\_id|String|vsw-2vc41xuumx5z2rdma\*\*\*,vsw-2vc41xuumx5z2rdma\*\*\*,vsw-2vc41xuumx5z2rdma\*\*\*\*,vsw-2vc41xuumx5z2rdma\*\*\*\*|The IDs of VSwitches in the VPC network. |
|worker\_ram\_role\_name|String|""|The name of the worker role of the cluster. |
|zone\_id|String|cn-chengdu-b|The ID of the zone where the node is deployed. |
|page\_info|Struct| |Information about the returned page. |
|page\_number|Integer|1|The page number of the returned page. |
|page\_size|Integer|50|The number of entries returned per page. |
|total\_count|Integer|1|The total number of entries. |

## Examples

Sample requests

```
GET /api/v1/clusters HTTP/1.1
Common request header
```

Sample success responses

`XML` format

```
<clusters>
    <name>test-cluster</name>
    <cluster_id>c6e31bbe4dd70499b8ff51aac0586****</cluster_id>
    <size>3</size>
    <region_id>cn-chengdu</region_id>
    <state>running</state>
    <cluster_type>Kubernetes</cluster_type>
    <created>2020-08-10T14:41:58+08:00</created>
    <updated>2020-08-14T16:17:38+08:00</updated>
    <init_version>1.16.9-aliyun.1</init_version>
    <current_version>1.16.9-aliyun.1</current_version>
    <meta_data/>
    <resource_group_id>rg-acfmyvw3wjm****</resource_group_id>
    <instance_type/>
    <vpc_id>vpc-2vcg932hsxsxuqbgl****</vpc_id>
    <vswitch_id>vsw-2vc41xuumx5z2rdma***,vsw-2vc41xuumx5z2rdma***,vsw-2vc41xuumx5z2rdma****,vsw-2vc41xuumx5z2rdma****</vswitch_id>
    <vswitch_cidr/>
    <data_disk_size>0</data_disk_size>
    <data_disk_category>cloud</data_disk_category>
    <security_group_id>sg-2vc6as1538wretr1****</security_group_id>
    <tags>
        <key>ack.aliyun.com</key>
        <value>c6e31bbe4dd70499b8ff51aac0586****</value>
    </tags>
    <zone_id>cn-chengdu-b</zone_id>
    <->PayByTraffic</->
    <network_mode>vpc</network_mode>
    <subnet_cidr>172.20.0.0/16</subnet_cidr>
    <master_url/>
    <external_loadbalancer_id/>
    <port>0</port>
    <node_status/>
    <cluster_healthy/>
    <docker_version>19.03.5</docker_version>
    <swarm_mode>false</swarm_mode>
    <gw_bridge/>
    <upgrade_components/>
    <next_version/>
    <private_zone>false</private_zone>
    <profile>Default</profile>
    <deletion_protection>false</deletion_protection>
    <cluster_spec/>
    <capabilities/>
    <enabled_migration>false</enabled_migration>
    <need_update_agent>false</need_update_agent>
    <outputs/>
    <parameters/>
    <worker_ram_role_name/>
</clusters>
<page_info>
    <page_number>1</page_number>
    <page_size>50</page_size>
    <total_count>1</total_count>
</page_info>
```

`JSON` format

```
{
    "clusters": [
        {
            "name": "test-cluster",
            "cluster_id": "c6e31bbe4dd70499b8ff51aac0586****",
            "size": 3,
            "region_id": "cn-chengdu",
            "state": "running",
            "cluster_type": "Kubernetes",
            "created": "2020-08-10T14:41:58+08:00",
            "updated": "2020-08-14T16:17:38+08:00",
            "init_version": "1.16.9-aliyun.1",
            "current_version": "1.16.9-aliyun.1",
            "meta_data": "",
            "resource_group_id": "rg-acfmyvw3wjm****",
            "instance_type": "",
            "vpc_id": "vpc-2vcg932hsxsxuqbgl****",
            "vswitch_id": "vsw-2vc41xuumx5z2rdma***,vsw-2vc41xuumx5z2rdma***,vsw-2vc41xuumx5z2rdma****,vsw-2vc41xuumx5z2rdma****",
            "vswitch_cidr": "",
            "data_disk_size": 0,
            "data_disk_category": "cloud",
            "security_group_id": "sg-2vc6as1538wretr1****",
            "tags": [
                {
                    "key": "ack.aliyun.com",
                    "value": "c6e31bbe4dd70499b8ff51aac0586****"
                }
            ],
            "zone_id": "cn-chengdu-b",
            "-": "PayByTraffic",
            "network_mode": "vpc",
            "subnet_cidr": "172.20.0.0/16",
            "master_url": "",
            "external_loadbalancer_id": "",
            "port": 0,
            "node_status": "",
            "cluster_healthy": "",
            "docker_version": "19.03.5",
            "swarm_mode": false,
            "gw_bridge": "",
            "upgrade_components": null,
            "next_version": "",
            "private_zone": false,
            "profile": "Default",
            "deletion_protection": false,
            "cluster_spec": "",
            "capabilities": null,
            "enabled_migration": false,
            "need_update_agent": false,
            "outputs": null,
            "parameters": null,
            "worker_ram_role_name": ""
        }
    ],
    "page_info": {
        "page_number": 1,
        "page_size": 50,
        "total_count": 1
    }
}
```

## Errors codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

