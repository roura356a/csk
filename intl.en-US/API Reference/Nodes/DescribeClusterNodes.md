# DescribeClusterNodes

Queries information about the nodes in a specified Container Service for Kubernetes \(ACK\) cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DescribeClusterNodes&type=ROA&version=2015-12-15)

## Request syntax

```
GET /clusters/ClusterId/nodes?instanceIds=["String"]&nodepool_id=String&state=String&pageSize=String&pageNumber=String HTTP/1.1 
Content-Type:application/json
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|c82e6987e2961451182edacd74faf\*\*\*\*|The ID of the ACK cluster. |

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|instanceIds|Array of String|No|i-2ze96kn383d1mz1q\*\*\*\*|The IDs of Elastic Compute Service \(ECS\) instances. Instances that match the IDs are returned.

 If nodepool\_id is not empty, this parameter is ignored. |
|nodepool\_id|String|No|np\*\*\*\*|The ID of the node pool. |
|state|String|No|running|The node state. You can filter the nodes by state. Valid values:

 -   `all`: queries all nodes in the following four states.
-   `running`: queries nodes that are running.
-   `removing`: queries nodes that are being removed.
-   `initial`: queries nodes that are being initialized.
-   `failed`: queries nodes that failed to be created.

 Default value: `all`. |
|pageSize|String|No|3|The number of entries to return on each page. |
|pageNumber|String|No|50|The number of the page to return. |

## Response syntax

```
HTTP/1.1 200 OK
Content-Type:application/json

{
  "nodes" : [ {
    "creation_time" : "String",
    "error_message" : "String",
    "expired_time" : "String",
    "host_name" : "String",
    "image_id" : "String",
    "instance_charge_type" : "String",
    "instance_id" : "String",
    "instance_name" : "String",
    "instance_role" : "String",
    "instance_status" : "String",
    "instance_type" : "String",
    "instance_type_family" : "String",
    "ip_address" : [ "String" ],
    "is_aliyun_node" : Boolean,
    "node_name" : "String",
    "node_status" : "String",
    "nodepool_id" : "String",
    "source" : "String",
    "state" : "String",
    "spot_strategy" : "String"
  } ],
  "page" : {
    "page_number" : Integer,
    "page_size" : Integer,
    "total_count" : Integer
  }
}
```

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|nodes|Array of node| |The details of the nodes that were queried. |
|creation\_time|String|2020-08-25T11:25:35+08:00|The time when the node was created. |
|error\_message|String|error\*\*\*|The error message generated when the node was created. |
|expired\_time|String|2099-12-31T23:59:00+08:00|The expiration time of the node. |
|host\_name|String|iZ2vcckdmxp7u0urj2k\*\*\*\*|The name of the host. |
|image\_id|String|aliyun\_2\_1903\_x64\_20G\_alibase\_20200529.vhd|The ID of the system image that is used by the node. |
|instance\_charge\_type|String|PostPaid|The billing method of the node. Valid values:

 -   `PrePaid`: the subscription billing method. If the value is PrePaid, make sure that you have sufficient balance or credit in your account. Otherwise, an `InvalidPayMethod` error is returned.
-   `PostPaid`: the pay-as-you-go billing method. |
|instance\_id|String|i-2vcckdmxp7u0urj2\*\*\*\*|The ID of the ECS instance. |
|instance\_name|String|worker-k8s-for-cs-c5cdf7e3938bc4f8eb0e44b21a80f\*\*\*\*|The name of the node. This name contains the ID of the cluster to which the node is deployed. |
|instance\_role|String|Worker|The role of the node. Valid values:

 -   Master: a master node.
-   Worker: a worker node. |
|instance\_status|String|Running|The state of the node. |
|instance\_type|String|ecs.c5.xlarge|The instance type of the node. |
|instance\_type\_family|String|ecs.c5|The ECS instance family of the node. |
|ip\_address|Array of String|192.168.0.36|The IP address of the node. |
|is\_aliyun\_node|Boolean|true|Indicates whether the instance is provided by Alibaba Cloud. Valid values:

 -   `true`: The instance is provided by Alibaba Cloud.
-   `false`: The instance is not provided by Alibaba Cloud. |
|node\_name|String|cn-chengdu.192.168.0.36|The name of the node in the ACK cluster. This node name is the identifier of the node in the cluster. |
|node\_status|String|Ready|Indicates whether the node is ready in the ACK cluster. Valid values:

 -   `true`: The node is ready.
-   `false`: The node is not ready. |
|nodepool\_id|String|np0794239424a84eb7a95327369d56\*\*\*\*|The ID of the node pool. |
|source|String|ess\_attach|Indicates how the nodes in the node pool were initialized. The nodes can be manually created or created by using Resource Orchestration Service \(ROS\). |
|state|String|running|The states of the nodes in the node pool. Valid values:

 -   `pending`: The nodes are being created.
-   `running`: The nodes are running.
-   `starting`: The nodes are being started.
-   `stopping`: The nodes are being stopped.
-   `stopped`: The nodes are stopped. |
|spot\_strategy|String|NoSpot|Indicates whether preemptible instances are used. Valid values:

 -   `NoSpot`: Preemptible instances are not used.
-   `SpotWithPriceLimit`: If `spot_strategy` is set to `SpotWithPriceLimit`, the system uses preemptible instances within a price limit. You must specify an `upper limit for the price`.
-   `SpotAsPriceGo`: If `spot_strategy` is set to `SpotAsPriceGo`, it indicates that the system bids based on the current market price of a preemptible instance. |
|page|object| |The pagination details. |
|page\_number|Integer|1|The number of the returned page. |
|page\_size|Integer|10|The number of entries returned per page. |
|total\_count|Integer|5|The total number of the returned entries. |

## Examples

Sample requests

```
GET /clusters/c82e6987e2961451182edacd74faf****/nodes?instanceIds=["i-2ze96kn383d1mz1q****"]&nodepool_id=np***&state=running&pageSize=3&pageNumber=50 HTTP/1.1 
Content-Type:application/json
```

Sample success responses

`XML` format

```
HTTP/1.1 200 OK
Content-Type:application/xml

<nodes>
    <creation_time>2020-08-25T11:25:35+08:00</creation_time>
    <error_message>***</error_message>
    <expired_time>2099-12-31T23:59:00+08:00</expired_time>
    <host_name>iZ2vcckdmxp7u0urj2k****</host_name>
    <image_id>aliyun_2_1903_x64_20G_alibase_20200529.vhd</image_id>
    <instance_charge_type>PostPaid</instance_charge_type>
    <instance_id>i-2vcckdmxp7u0urj2****</instance_id>
    <instance_name>worker-k8s-for-cs-c5cdf7e3938bc4f8eb0e44b21a80f****</instance_name>
    <instance_role>Worker</instance_role>
    <instance_status>Running</instance_status>
    <instance_type>ecs.c5.xlarge</instance_type>
    <instance_type_family>ecs.c5</instance_type_family>
    <ip_address>192.168.0.36</ip_address>
    <is_aliyun_node>true</is_aliyun_node>
    <node_name>cn-chengdu.192.168.0.36</node_name>
    <node_status>Ready</node_status>
    <nodepool_id>np0794239424a84eb7a95327369d56****</nodepool_id>
    <source>ess_attach</source>
    <state>running</state>
    <spot_strategy>NoSpot</spot_strategy>
</nodes>
<page>
    <page_number>1</page_number>
    <page_size>10</page_size>
    <total_count>5</total_count>
</page>
```

`JSON` format

```
HTTP/1.1 200 OK
Content-Type:application/json

{
  "nodes" : [ {
    "creation_time" : "2020-08-25T11:25:35+08:00",
    "error_message" : "***",
    "expired_time" : "2099-12-31T23:59:00+08:00",
    "host_name" : "iZ2vcckdmxp7u0urj2k****",
    "image_id" : "aliyun_2_1903_x64_20G_alibase_20200529.vhd",
    "instance_charge_type" : "PostPaid",
    "instance_id" : "i-2vcckdmxp7u0urj2****",
    "instance_name" : "worker-k8s-for-cs-c5cdf7e3938bc4f8eb0e44b21a80f****",
    "instance_role" : "Worker",
    "instance_status" : "Running",
    "instance_type" : "ecs.c5.xlarge",
    "instance_type_family" : "ecs.c5",
    "ip_address" : [ "192.168.0.36" ],
    "is_aliyun_node" : true,
    "node_name" : "cn-chengdu.192.168.0.36",
    "node_status" : "Ready",
    "nodepool_id" : "np0794239424a84eb7a95327369d56****",
    "source" : "ess_attach",
    "state" : "running",
    "spot_strategy" : "NoSpot"
  } ],
  "page" : {
    "page_number" : 1,
    "page_size" : 10,
    "total_count" : 5
  }
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

