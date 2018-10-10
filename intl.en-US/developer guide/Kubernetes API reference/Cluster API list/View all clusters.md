# View all clusters {#reference_brm_5pb_wdb .reference}

View all the clusters you have created in Container Service, including swarm clusters and Kubernetes clusters.

## Request information {#section_whb_xpb_wdb .section}

**Request line \(RequestLine\)**

```
GET /clusters HTTP/1.1
```

**Special request header \(RequestHead\)**

None. See [Public request headers](intl.en-US/Developer Guide/Kubernetes API reference/Cluster API call method/Common parameters.md#section_mr5_lf1_wdb).

**Request body \(RequestBody\)**

None. 

## Response information {#section_ddh_bqb_wdb .section}

**Response line \(ResponseLine\)**

```
HTTP/1.1 200 OK
```

**Special response header \(ResponseHead\)**

None. See [Public response headers](intl.en-US/Developer Guide/Kubernetes API reference/Cluster API call method/Common parameters.md#section_zr5_lf1_wdb).

**Response body \(ResponseBody\)**

```
[
    {
        "agent_version": "string",
        "cluster_id": "string",
        "created": "datetime",
        "external_loadbalancer_id": "string",
        "master_url": "string",
        "name": "string",
        "network_mode": "string",
        "region_id": "string",
        "security_group_id": "string",
        "size": "numbers",
        "state": "string",
        "updated": "datetime",
        "vpc_id": "string",
        "vswitch_id": "string"
    }
]
```

**Response body explanation**

Cluster format

|Name|Type|Description|
|:---|:---|:----------|
|agent\_version|string|The Agent version.|
|cluster\_id|string|The cluster ID, which is the unique identifier of the cluster.|
|created|string|The creation time of the cluster.|
|external\_loadbalancer\_id|string|The Server Load Balancer instance ID of the cluster.|
|master\_url|string|The master address of the cluster, which is used to connect to the cluster to perform operations. For more information, see [Connect to a Kubernetes cluster by using kubectl](../../../../intl.en-US/User Guide/Kubernetes cluster/Clusters/Connect to a Kubernetes cluster by using kubectl.md#).|
|name|string|The cluster name, which is specified when you create the cluster and is unique for each account.|
|network\_mode|string|The network mode of the cluster \(Virtual Private Cloud \(VPC\)\).|
|region\_id|string|The ID of the region where the cluster is located.|
|security\_group\_id|string|The security group ID.|
|size|string|The number of nodes.|
|state|string|The cluster status. For more information, see [Cluster lifecycle](../../../../intl.en-US/User Guide/Swarm cluster/Clusters/Cluster lifecycle.md#).|
|updated|string|The last update time.|
|vpc\_id|string|The VPC ID.|
|vswitch\_id|string|The VSwitch ID.|

## Example {#section_h3k_rqb_wdb .section}

**Request example**

```
GET /clusters HTTP/1.1
<Public request header>
```

**Response example**

```
HTTP/1.1 200 OK
<Public response header>
[
    {
        "agent_version": "0.5-e56dab3",
        "cluster_id": "c978ca3eaacd3409a9437db07598f1f69",
        "created": "2015-12-11T03:52:40Z",
        "external_loadbalancer_id": "1518f2b7e4c-cn-beijing-btc-a01",
        "master_url": "https://182.92.245.56:17589",
        "name": "my-python-cluster-039de960",
        "network_mode": "vpc",
        "region_id": "cn-beijing",
        "security_group_id": "sg-25yqjuxhz",
        "size": 5,
        "state": "running",
        "updated": "2015-12-15T15:01:58Z",
        "vpc_id": "",
        "vswitch_id": ""
    },
    {
        "agent_version": "0.5-e56dab3",
        "cluster_id": "c1eb19e0093204cbb86c3a80334d2129e",
        "created": "2015-12-15T14:26:58Z",
        "external_loadbalancer_id": "151a6099de1-cn-beijing-btc-a01",
        "master_url": "https://182.92.245.56:11905",
        "name": "my-test-cluster-002b3f3d",
        "network_mode": "vpc",
        "region_id": "cn-beijing",
        "security_group_id": "sg-25rg2ws9f",
        "size": 1,
        "state": "running",
        "updated": "2015-12-15T14:43:55Z",
        "vpc_id": "",
        "vswitch_id": ""
    }
]
```

