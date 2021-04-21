# DescribeClusterDetail

Queries the details of a specific Container Service for Kubernetes \(ACK\) cluster by cluster ID.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DescribeClusterDetail&type=ROA&version=2015-12-15)

## Request syntax

```
GET /clusters/ClusterId HTTP/1.1 
Content-Type:application/json
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|cdde1f21ae22e483ebcb068a6eb7f\*\*\*\*|The ID of the ACK cluster that you want to query. |

## Response syntax

```
HTTP/1.1 200
Content-Type:application/json
{
  "cluster_id" : "String",
  "cluster_type" : "String",
  "created" : "String",
  "init_version" : "String",
  "current_version" : "String",
  "next_version" : "String",
  "deletion_protection" : Boolean,
  "docker_version" : "String",
  "external_loadbalancer_id" : "String",
  "meta_data" : "String",
  "name" : "String",
  "network_mode" : "String",
  "region_id" : "String",
  "resource_group_id" : "String",
  "security_group_id" : "String",
  "size" : Long,
  "state" : "String",
  "tags" : [ {
    "key" : "String",
    "value" : "String"
  } ],
  "updated" : "String",
  "vpc_id" : "String",
  "vswitch_id" : "String",
  "subnet_cidr" : "String",
  "zone_id" : "String",
  "master_url" : "String",
  "private_zone" : Boolean,
  "profile" : "String",
  "cluster_spec" : "String",
  "worker_ram_role_name" : "String",
  "maintenance_window" : {
    "enable" : Boolean,
    "maintenance_time" : "String",
    "duration" : "String",
    "weekly_period" : "String"
  }
}
```

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|cluster\_id|String|c82e6987e2961451182edacd74faf\*\*\*\*|The ID of the queried ACK cluster. |
|cluster\_type|String|Kubernetes|The type of the cluster. Valid values:

 -   `Kubernetes`: dedicated Kubernetes cluster
-   `ManagedKubernetes`: managed Kubernetes cluster
-   `Ask`: serverless Kubernetes \(ASK\) cluster
-   `ExternalKubernetes`: registered external Kubernetes cluster |
|created|String|2019-11-25T15:50:20+08:00|The time when the cluster was created. |
|init\_version|String|1.16.6-aliyun.1|The Kubernetes version that is initially used by the cluster. |
|current\_version|String|1.16.6-aliyun.1|The current Kubernetes version of the cluster. For more information about the Kubernetes versions supported by ACK, see [Release notes for Kubernetes versions](~~185269~~). |
|next\_version|String|1.18.8-aliyun.1|The Kubernetes version to which the cluster can be upgraded. |
|deletion\_protection|Boolean|true|Indicates whether deletion protection is enabled. If deletion protection is enabled, the cluster cannot be deleted in the ACK console or by calling the API. Valid values:

 -   `true`: Deletion protection is enabled. You cannot delete the cluster in the ACK console or by calling the API.
-   `false`: Deletion protection is not enabled. You can delete the cluster in the ACK console or by calling the API. |
|docker\_version|String|19.03.5|The Docker version that is used by the cluster. |
|external\_loadbalancer\_id|String|lb-2ze3buguz3gx9920z\*\*\*\*|The ID of the Server Load Balancer \(SLB\) instance that is used for the Ingress of the cluster. |
|meta\_data|String|\\"Addons\\":\*\*\*|The metadata of the cluster. |
|name|String|cluster-demo|The name of the cluster.

 The name must be 1 to 63 characters in length, and can contain digits, letters, and hyphens \(-\). It cannot start with a hyphen \(-\). |
|network\_mode|String|vpc|The network mode of the cluster. Valid values:

 -   `classic`: the classic network
-   `vpc`: virtual private cloud \(VPC\)
-   `overlay`: overlay network
-   `calico`: network powered by Calico

 Default value`: vpc`. |
|region\_id|String|cn-beijing|The ID of the region where the cluster is deployed. |
|resource\_group\_id|String|rg-acfmyvw3wjm\*\*\*\*|The ID of the resource group to which the cluster belongs. |
|security\_group\_id|String|sg-25yq\*\*\*\*|The ID of the security group to which the instances of the cluster belong. |
|size|Long|5|The number of nodes in the cluster. Master nodes and worker nodes are included. |
|state|String|running|The state of the cluster. Valid values:

 -   `initial`: The cluster is being created.
-   `failed`: The cluster failed to be created.
-   `running`: The cluster is running.
-   `updating`: The cluster is being upgraded.
-   `updating_failed`: The cluster failed to be upgraded.
-   `scaling`: The cluster is being scaled.
-   `stopped`: The cluster is stopped.
-   `deleting`: The cluster is being deleted.
-   `deleted`: The cluster is deleted.
-   `delete_failed`: The cluster failed to be deleted. |
|tags|Array of [tag](/intl.en-US/API Reference/Commonly used parameters.md)| |The labels of the cluster. |
|updated|String|2020-01-13T23:01:03+08:00|The time when the cluster was updated. |
|vpc\_id|String|vpc-2zecuu62b9zw7a7qn\*\*\*\*|The ID of the VPC where the cluster is deployed. This parameter is required when you create an ACK cluster. |
|vswitch\_id|String|vsw-2zete8s4qocqg0mf6\*\*\*\*,vsw-2zete8s4qocqg0mf6\*\*\*\*|The IDs of the vSwitches. You can select one to three vSwitches when you create an ACK cluster. vSwitches in different zones are recommended to ensure high availability. |
|subnet\_cidr|String|172.20.0.0/16|The pod CIDR block. It must be a valid and private CIDR block, and must be one of the following CIDR blocks or their subnets:

 -   10.0.0.0/8
-   172.16-31.0.0/12-16
-   192.168.0.0/16

 The pod CIDR block cannot overlap with that of the VPC or those of the ACK clusters that are deployed in the VPC.

 For more information about the network segmentation of ACK clusters, see [Plan CIDR blocks for ACK clusters in a VPC](~~6500~~). |
|zone\_id|String|cn-beijing-a|The ID of the zone where the cluster is deployed. |
|master\_url|String|\{\\"intranet\_api\_server\_endpoint\\":\\"https://192.168.0.251:6443\\"\*\*\*\}|The address of the cluster. It includes an internal endpoint and a public endpoint. |
|private\_zone|Boolean|false|Indicates whether Alibaba Cloud DNS PrivateZone is enabled.

 -   `true`: indicates that Alibaba Cloud DNS PrivateZone is enabled.
-   `false`: indicates that Alibaba Cloud DNS PrivateZone is not enabled. |
|profile|String|Default|Indicates the scenario in which the cluster is used. Valid values:

 -   `Default`: indicates that the cluster is used in non-edge computing scenarios.
-   `Edge`: indicates that the ACK cluster is used in edge computing scenarios. |
|cluster\_spec|String|ack.pro.small|The type of the managed Kubernetes cluster. This parameter is returned for a managed Kubernetes cluster. Valid values:

 -   `ack.pro.small`: professional managed Kubernetes cluster.
-   `ack.standard`: standard managed Kubernetes cluster. |
|worker\_ram\_role\_name|String|KubernetesWorkerRole-ec87d15b-edca-4302-933f-c8a16bf0\*\*\*\*|The name of the worker RAM role. The RAM role is assigned to the worker nodes of the cluster and allows the worker nodes to manage Elastic Compute Service \(ECS\) instances. |
|maintenance\_window|[maintenance\_window](/intl.en-US/API Reference/Commonly used parameters.md)| |The maintenance window of the cluster. This feature is available in only professional managed Kubernetes clusters. |

## Examples

Sample requests

```
GET /clusters/cdde1f21ae22e483ebcb068a6eb7f**** HTTP/1.1 
Content-Type:application/json
```

Sample success responses

`XML` format

```
HTTP/1.1 200 OK
Content-Type:application/xml

<cluster_id>c82e6987e2961451182edacd74faf****</cluster_id>
<cluster_type>Kubernetes</cluster_type>
<created>2019-11-25T15:50:20+08:00</created>
<init_version>1.16.6-aliyun.1</init_version>
<current_version>1.16.6-aliyun.1</current_version>
<next_version>1.18.8-aliyun.1</next_version>
<deletion_protection>true</deletion_protection>
<docker_version>19.03.5</docker_version>
<external_loadbalancer_id>lb-2ze3buguz3gx9920z****</external_loadbalancer_id>
<meta_data>\"Addons\":***</meta_data>
<name>cluster-demo</name>
<network_mode>vpc</network_mode>
<region_id>cn-beijing</region_id>
<resource_group_id>rg-acfmyvw3wjm****</resource_group_id>
<security_group_id>sg-25yq****</security_group_id>
<size>5</size>
<state>running</state>
<tags>
    <key>env</key>
    <value>prod</value>
</tags>
<updated>2020-01-13T23:01:03+08:00</updated>
<vpc_id>vpc-2zecuu62b9zw7a7qn****</vpc_id>
<vswitch_id>vsw-2zete8s4qocqg0mf6****,vsw-2zete8s4qocqg0mf6****</vswitch_id>
<subnet_cidr>172.20.0.0/16</subnet_cidr>
<zone_id>cn-beijing-a</zone_id>
<master_url>{\"intranet_api_server_endpoint\":\"https://192.168.0.251:6443\"***}</master_url>
<private_zone>false</private_zone>
<profile>Default</profile>
<cluster_spec>ack.pro.small</cluster_spec>
<worker_ram_role_name>KubernetesWorkerRole-ec87d15b-edca-4302-933f-c8a16bf0****</worker_ram_role_name>
<maintenance_window>
    <enable>false</enable>
    <maintenance_time>03:00:00Z</maintenance_time>
    <duration>3h</duration>
    <weekly_period>Monday,Thursday</weekly_period>
</maintenance_window>
```

`JSON` format

```
HTTP/1.1 200 OK
Content-Type:application/json

{
  "cluster_id" : "c82e6987e2961451182edacd74faf****",
  "cluster_type" : "Kubernetes",
  "created" : "2019-11-25T15:50:20+08:00",
  "init_version" : "1.16.6-aliyun.1",
  "current_version" : "1.16.6-aliyun.1",
  "next_version" : "1.18.8-aliyun.1",
  "deletion_protection" : true,
  "docker_version" : "19.03.5",
  "external_loadbalancer_id" : "lb-2ze3buguz3gx9920z****",
  "meta_data" : "\\\"Addons\\\":***",
  "name" : "cluster-demo",
  "network_mode" : "vpc",
  "region_id" : "cn-beijing",
  "resource_group_id" : "rg-acfmyvw3wjm****",
  "security_group_id" : "sg-25yq****",
  "size" : 5,
  "state" : "running",
  "tags" : [ {
    "key" : "env",
    "value" : "prod"
  } ],
  "updated" : "2020-01-13T23:01:03+08:00",
  "vpc_id" : "vpc-2zecuu62b9zw7a7qn****",
  "vswitch_id" : "vsw-2zete8s4qocqg0mf6****,vsw-2zete8s4qocqg0mf6****",
  "subnet_cidr" : "172.20.0.0/16",
  "zone_id" : "cn-beijing-a",
  "master_url" : "{\\\"intranet_api_server_endpoint\\\":\\\"https://192.168.0.251:6443\\\"***}",
  "private_zone" : false,
  "profile" : "Default",
  "cluster_spec" : "ack.pro.small",
  "worker_ram_role_name" : "KubernetesWorkerRole-ec87d15b-edca-4302-933f-c8a16bf0****",
  "maintenance_window" : {
    "enable" : false,
    "maintenance_time" : "03:00:00Z",
    "duration" : "3h",
    "weekly_period" : "Monday,Thursday"
  }
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

