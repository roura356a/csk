# Query nodes in a cluster

You can call DescribeClusterNodes to query nodes in a cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DescribeClusterNodes&type=ROA&version=2015-12-15)

## Request parameter types

This operation uses only common request parameters. For more information, see Common parameters.

## Request syntax

```
get /clusters/{ClusterId}/nodes http|https
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|c82e6987e2961451182edacd74faf\*\*\*\*|The ID of the cluster. |
|pageSize|String|No|3|The number of entries to return on each page. |
|pageNumber|String|No|50|The total number of pages to return. |
|nodepool\_id|String|No|np\*\*\*|The ID of the node pool. |
|state|String|No|all|The status of nodes in the cluster. You can filter nodes by node status. Valid values:

 -   all: Query all nodes in the cluster.
-   running: Query nodes that are running in the cluster.
-   removing: Query nodes that are being removed from the cluster.
-   initial: Query nodes that are initializing in the cluster.
-   failed: Query nodes that failed to be created in the cluster. |

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|nodes|Array of node| |A list of nodes. |
|creation\_time|String|"2020-08-25T11:25:35+08:00"|The time when the node was created. |
|error\_message|String|"\*\*\*"|The error message about the node. |
|expired\_time|String|"2099-12-31T23:59:00+08:00"|The expiration time of the node. |
|host\_name|String|"iZ2vcckdmxp7u0urj2k\*\*\*\*"|The name of the host. |
|image\_id|String|"aliyun\_2\_1903\_x64\_20G\_alibase\_20200529.vhd"|The ID of the system image used by the node. |
|instance\_charge\_type|String|"PostPaid"|The billing method of the node. |
|instance\_id|String|"i-2vcckdmxp7u0urj2k2rw"|The ID of the ECS instance. |
|instance\_name|String|"worker-k8s-for-cs-c5cdf7e3938bc4f8eb0e44b21a80f\*\*\*\*"|The name of the ECS instance. |
|instance\_role|String|"Worker"|The role of the node. Valid values:

 -   Master
-   Worker |
|instance\_status|String|"Running"|The status of the ECS instance. |
|instance\_type|String|"ecs.c5.xlarge"|The ECS instance type of the node. |
|instance\_type\_family|String|"ecs.c5"|The ECS instance family of the node. |
|ip\_address|Array of String| |The IP address of the node. |
|is\_aliyun\_node|Boolean|"true"|Indicates whether the node is provided by Alibaba Cloud. |
|node\_name|String|"cn-chengdu.192.168.0.36"|The name of the node in the cluster. |
|node\_status|String|"Ready"|The status of the node in the cluster. |
|nodepool\_id|String|"np0794239424a84eb7a95327369d56\*\*\*\*"|The ID of the node pool. |
|source|String|"ess\_attach"|The source of the node. You can add a node to a cluster in the Container Service for Kubernetes \(ACK\) console or Resource Orchestration Service \(ROS\) console. |
|state|String|"running"|The status of the ECS instance. |
|page|Object| |The pagination details. |
|page\_number|Integer|"1"|The total number of pages returned. |
|page\_size|Integer|"10"|The number of entries returned on each page. |
|total\_count|Integer|"5"|The total number of entries returned. |

## Examples

Sample requests

```
GET /clusters/[ClusterId]/nodes HTTP/1.1
Common request parameters
{
    "ClusterId":" c82e6987e2961451182edacd74faf****"
}
```

Sample success responses

`XML` format

```
<nodes>
    <creation_time>2020-08-25T11:25:35+08:00</creation_time>
    <error_message/>
    <instance_name>worker-k8s-for-cs-c5cdf7e3938bc4f8eb0e44b21a80f****</instance_name>
    <node_status>Ready</node_status>
    <is_aliyun_node>true</is_aliyun_node>
    <node_name>cn-chengdu.192.168.0.36</node_name>
    <expired_time>2099-12-31T23:59:00+08:00</expired_time>
    <ip_address>192.168.0.36</ip_address>
    <source>ess_attach</source>
    <instance_type_family>ecs.c5</instance_type_family>
    <instance_id>i-2vcckdmxp7u0urj2k2rw</instance_id>
    <instance_charge_type>PostPaid</instance_charge_type>
    <instance_role>Worker</instance_role>
    <instance_status>Running</instance_status>
    <state>running</state>
    <image_id>aliyun_2_1903_x64_20G_alibase_20200529.vhd</image_id>
    <instance_type>ecs.c5.xlarge</instance_type>
    <nodepool_id>np0794239424a84eb7a95327369d56****</nodepool_id>
    <host_name>iZ2vcckdmxp7u0urj2k****</host_name>
</nodes>
<page>
    <page_number>1</page_number>
    <total_count>5</total_count>
    <page_size>10</page_size>
</page>
```

`JSON` format

```
{
    "nodes": [
        {
            "creation_time": "2020-08-25T11:25:35+08:00",
            "error_message": "",
            "instance_name": "worker-k8s-for-cs-c5cdf7e3938bc4f8eb0e44b21a80f****",
            "node_status": "Ready",
            "is_aliyun_node": "true",
            "node_name": "cn-chengdu.192.168.0.36",
            "expired_time": "2099-12-31T23:59:00+08:00",
            "ip_address": "192.168.0.36",
            "source": "ess_attach",
            "instance_type_family": "ecs.c5",
            "instance_id": "i-2vcckdmxp7u0urj2k2rw",
            "instance_charge_type": "PostPaid",
            "instance_role": "Worker",
            "instance_status": "Running",
            "state": "running",
            "image_id": "aliyun_2_1903_x64_20G_alibase_20200529.vhd",
            "instance_type": "ecs.c5.xlarge",
            "nodepool_id": "np0794239424a84eb7a95327369d56****",
            "host_name": "iZ2vcckdmxp7u0urj2k****"
        }
    ],
    "page": {
        "page_number": "1",
        "total_count": "5",
        "page_size": "10"
    }
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

