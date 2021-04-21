# DescribeClustersV1

Queries the details of a specified Container Service for Kubernetes \(ACK\) cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DescribeClustersV1&type=ROA&version=2015-12-15)

## Request syntax

```
GET /api/v1/clusters?name=String&cluster_type=String&page_size=Long&page_number=Long HTTP/1.1 
Content-Type:application/json
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|name|String|No|cluster-demo|The name of the ACK cluster.

 The name must be 1 to 63 characters in length, and can contain digits, letters, and hyphens \(-\). It cannot start with a hyphen \(-\). |
|cluster\_type|String|No|Kubernetes|The type of the cluster. Valid values:

 -   `Kubernetes`: dedicated Kubernetes cluster
-   `ManagedKubernetes`: managed Kubernetes cluster
-   `Ask`: serverless Kubernetes \(ASK\) cluster
-   `ExternalKubernetes`: registered external Kubernetes cluster |
|page\_size|Long|No|10|The number of entries to return on each page. |
|page\_number|Long|No|3|The number of the page to return. |

## Response syntax

```
HTTP/1.1 200
Content-Type:application/json
{
  "clusters" : [ {
    "cluster_id" : "String",
    "cluster_type" : "String",
    "created" : "String",
    "init_version" : "String",
    "current_version" : "String",
    "next_version" : "String",
    "deletion_protection" : Boolean,
    "docker_version" : "String",
    "external_loadbalancer_id" : "String",
    "master_url" : "String",
    "meta_data" : "String",
    "name" : "String",
    "network_mode" : "String",
    "private_zone" : Boolean,
    "profile" : "String",
    "region_id" : "String",
    "resource_group_id" : "String",
    "security_group_id" : "String",
    "size" : Long,
    "state" : "String",
    "subnet_cidr" : "String",
    "tags" : [ {
      "key" : "String",
      "value" : "String"
    } ],
    "updated" : "String",
    "vpc_id" : "String",
    "vswitch_id" : "String",
    "worker_ram_role_name" : "String",
    "zone_id" : "String",
    "cluster_spec" : "String",
    "maintenance_window" : {
      "enable" : Boolean,
      "maintenance_time" : "String",
      "duration" : "String",
      "weekly_period" : "String"
    }
  } ],
  "page_info" : {
    "page_number" : Integer,
    "page_size" : Integer,
    "total_count" : Integer
  }
}
```

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|clusters|Array| |The list of the details of the queried cluster. |
|cluster\_id|String|c3fb96524f9274b4495df0f12a6b5\*\*\*\*|The ID of the ACK cluster. |
|cluster\_type|String|Kubernetes|The type of the cluster. Valid values:

 -   `Kubernetes`: dedicated Kubernetes cluster
-   `ManagedKubernetes`: managed Kubernetes cluster
-   `Ask`: ASK cluster
-   `ExternalKubernetes`: registered external Kubernetes cluster |
|created|String|2020-08-20T10:51:29+08:00|The time when the cluster was created. |
|init\_version|String|1.16.9-aliyun.1|The Kubernetes version of the cluster. The value is an open source Kubernetes version. The latest version is recommended. If you do not specify a Kubernetes version, the latest Kubernetes version is queried.

 You can create ACK clusters of the latest two Kubernetes versions in the ACK console. You can also create ACK clusters of other Kubernetes versions by calling the API. For more information about the Kubernetes versions supported by ACK, see [Release notes for Kubernetes versions](~~185269~~). |
|current\_version|String|1.16.9-aliyun.1|The Kubernetes version of the cluster. |
|next\_version|String|1.18.8-aliyun.1|The Kubernetes version to which the cluster can be upgraded. |
|deletion\_protection|Boolean|true|Indicates whether deletion protection is enabled. If deletion protection is enabled, the cluster cannot be deleted in the console or by calling the API. Valid values:

 -   `true`: Deletion protection is enabled. You cannot delete the cluster in the ACK console or by calling the API.
-   `false`: Deletion protection is not enabled. You can delete the cluster in the ACK console or by calling the API. |
|docker\_version|String|19.03.5|The Docker version that is used by the cluster. |
|external\_loadbalancer\_id|String|lb-2vcrbmlevo6kjpgch\*\*\*\*|The ID of the Server Load Balancer \(SLB\) instance that is used for the Ingress of the cluster.

 The default SLB specification is slb.s1.small, which belongs to the high-performance instance type. |
|master\_url|String|\{\\"api\_server\_endpoint\\":\\"\\",\\"intranet\_api\_server\_endpoint\\":\\"https://192.168.0.251:6443\\"\}|The address of the cluster API server. It includes an internal endpoint and a public endpoint. |
|meta\_data|String|\{\\"Addons\\":\[\{\\"config\\":\*\*\*\}|The metadata of the cluster. |
|name|String|cluster-demo|The name of the cluster.

 The name must be 1 to 63 characters in length, and can contain digits, letters, and hyphens \(-\). It cannot start with a hyphen \(-\). |
|network\_mode|String|vpc|The network mode of the cluster. Valid values:

 -   `classic`: the classic network
-   `vpc`: virtual private cloud \(VPC\)
-   `overlay`: overlay network
-   `calico`: network powered by Calico |
|private\_zone|Boolean|false|Indicates whether Alibaba Cloud DNS PrivateZone is enabled. Valid values:

 -   `true`: indicates that Alibaba Cloud DNS PrivateZone is enabled.
-   `false`: indicates that Alibaba Cloud DNS PrivateZone is not enabled. |
|profile|String|Default|The identifier of the cluster. Valid values:

 -   `Edge`: indicates that the cluster is a managed edge Kubernetes cluster.
-   `Default`: indicates that the cluster is not a managed edge Kubernetes cluster. |
|region\_id|String|cn-beijing|The ID of the region where the cluster is deployed. |
|resource\_group\_id|String|rg-acfmyvw3wjm\*\*\*\*|The ID of the resource group to which the cluster belongs. |
|security\_group\_id|String|sg-2vcgwsrwgt5mp0yi\*\*\*\*|The ID of the security group to which the instances of the cluster belong. |
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
|subnet\_cidr|String|172.21.0.0/16|The pod CIDR block. It must be a valid and private CIDR block, and must be one of the following CIDR blocks or their subnets:

 -   10.0.0.0/8
-   172.16-31.0.0/12-16
-   192.168.0.0/16

 The pod CIDR block cannot overlap with that of the VPC or those of the ACK clusters that are deployed in the VPC.

 For more information about the network segmentation of ACK clusters, see [Plan CIDR blocks for ACK clusters in a VPC](~~86500~~). |
|tags|Array of [tag](/intl.en-US/API Reference/Commonly used parameters.md)| |The labels of the cluster. |
|updated|String|2020-09-16T11:09:55+08:00|The time when the cluster was updated. |
|vpc\_id|String|vpc-2vcg932hsxsxuqbgl\*\*\*\*|The ID of the VPC where the cluster is deployed. This parameter is required when you create an ACK cluster. |
|vswitch\_id|String|vsw-2vc41xuumx5z2rdma\*\*\*\*,vsw-2vc41xuumx5z2rdma\*\*\*\*|The IDs of the vSwitches. You can select one to three vSwitches when you create an ACK cluster. vSwitches in different zones are recommended to ensure high availability. |
|worker\_ram\_role\_name|String|KubernetesWorkerRole-ec87d15b-edca-4302-933f-c8a16bf0\*\*\*\*|The name of the worker RAM role. The RAM role is assigned to the worker nodes of the cluster and allows the worker nodes to manage Elastic Compute Service \(ECS\) instances. |
|zone\_id|String|cn-beijing-b|The ID of the zone where the cluster is deployed. |
|cluster\_spec|String|ack.standard|The type of the managed Kubernetes cluster. This parameter is returned for a managed Kubernetes cluster. Valid values:

 -   `ack.pro.small`: professional managed Kubernetes cluster
-   `ack.standard`: standard managed Kubernetes cluster |
|maintenance\_window|[maintenance\_window](/intl.en-US/API Reference/Commonly used parameters.md)| |The maintenance window of the cluster. This feature is available in only professional managed Kubernetes clusters. |
|page\_info|object| |The pagination details. |
|page\_number|Integer|3|The page number of the returned page. |
|page\_size|Integer|20|The number of entries returned per page. |
|total\_count|Integer|50|The total number of entries returned. |

## Examples

Sample requests

```
GET /api/v1/clusters?name=cluster-demo&cluster_type=Kubernetes&page_size=10&page_number=3 HTTP/1.1 
Content-Type:application/json
```

Sample success responses

`XML` format

```
HTTP/1.1 200 OK
Content-Type:application/xml

<clusters>
    <cluster_id>c3fb96524f9274b4495df0f12a6b5****</cluster_id>
    <cluster_type>Kubernetes</cluster_type>
    <created>2020-08-20T10:51:29+08:00</created>
    <init_version>1.16.9-aliyun.1</init_version>
    <current_version>1.16.9-aliyun.1</current_version>
    <next_version>1.18.8-aliyun.1</next_version>
    <deletion_protection>true</deletion_protection>
    <docker_version>19.03.5</docker_version>
    <external_loadbalancer_id>lb-2vcrbmlevo6kjpgch****</external_loadbalancer_id>
    <master_url>{\"api_server_endpoint\":\"\",\"intranet_api_server_endpoint\":\"https://192.168.0.251:6443\"}</master_url>
    <meta_data>{\"Addons\":[{\"config\":***}</meta_data>
    <name>cluster-demo</name>
    <network_mode>vpc</network_mode>
    <private_zone>false</private_zone>
    <profile>Default</profile>
    <region_id>cn-beijing</region_id>
    <resource_group_id>rg-acfmyvw3wjm****</resource_group_id>
    <security_group_id>sg-2vcgwsrwgt5mp0yi****</security_group_id>
    <size>5</size>
    <state>running</state>
    <subnet_cidr>172.21.0.0/16</subnet_cidr>
    <tags>
        <key>env</key>
        <value>prod</value>
    </tags>
    <updated>2020-09-16T11:09:55+08:00</updated>
    <vpc_id>vpc-2vcg932hsxsxuqbgl****</vpc_id>
    <vswitch_id>vsw-2vc41xuumx5z2rdma****,vsw-2vc41xuumx5z2rdma****</vswitch_id>
    <worker_ram_role_name>KubernetesWorkerRole-ec87d15b-edca-4302-933f-c8a16bf0****</worker_ram_role_name>
    <zone_id>cn-beijing-b</zone_id>
    <cluster_spec>ack.standard</cluster_spec>
    <maintenance_window>
        <enable>false</enable>
        <maintenance_time>03:00:00Z</maintenance_time>
        <duration>3h</duration>
        <weekly_period>Monday,Thursday</weekly_period>
    </maintenance_window>
</clusters>
<page_info>
    <page_number>3</page_number>
    <page_size>20</page_size>
    <total_count>50</total_count>
</page_info>
```

`JSON` format

```
HTTP/1.1 200 OK
Content-Type:application/json

{
  "clusters" : [ {
    "cluster_id" : "c3fb96524f9274b4495df0f12a6b5****",
    "cluster_type" : "Kubernetes",
    "created" : "2020-08-20T10:51:29+08:00",
    "init_version" : "1.16.9-aliyun.1",
    "current_version" : "1.16.9-aliyun.1",
    "next_version" : "1.18.8-aliyun.1",
    "deletion_protection" : true,
    "docker_version" : "19.03.5",
    "external_loadbalancer_id" : "lb-2vcrbmlevo6kjpgch****",
    "master_url" : "{\\\"api_server_endpoint\\\":\\\"\\\",\\\"intranet_api_server_endpoint\\\":\\\"https://192.168.0.251:6443\\\"}",
    "meta_data" : "{\\\"Addons\\\":[{\\\"config\\\":***}",
    "name" : "cluster-demo",
    "network_mode" : "vpc",
    "private_zone" : false,
    "profile" : "Default",
    "region_id" : "cn-beijing",
    "resource_group_id" : "rg-acfmyvw3wjm****",
    "security_group_id" : "sg-2vcgwsrwgt5mp0yi****",
    "size" : 5,
    "state" : "running",
    "subnet_cidr" : "172.21.0.0/16",
    "tags" : [ {
      "key" : "env",
      "value" : "prod"
    } ],
    "updated" : "2020-09-16T11:09:55+08:00",
    "vpc_id" : "vpc-2vcg932hsxsxuqbgl****",
    "vswitch_id" : "vsw-2vc41xuumx5z2rdma****,vsw-2vc41xuumx5z2rdma****",
    "worker_ram_role_name" : "KubernetesWorkerRole-ec87d15b-edca-4302-933f-c8a16bf0****",
    "zone_id" : "cn-beijing-b",
    "cluster_spec" : "ack.standard",
    "maintenance_window" : {
      "enable" : false,
      "maintenance_time" : "03:00:00Z",
      "duration" : "3h",
      "weekly_period" : "Monday,Thursday"
    }
  } ],
  "page_info" : {
    "page_number" : 3,
    "page_size" : 20,
    "total_count" : 50
  }
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

