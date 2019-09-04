# View a cluster {#reference_h4x_bnb_wdb .reference}

View the cluster details according to the cluster ID.

## Request Information {#section_rnj_2nb_wdb .section}

**Request line \(RequestLine\)**

``` {#codeblock_jgg_az1_fly}
GET /clusters/{cluster_id} HTTP/1.1
```

**Request line parameter \(URI Param\)**

|Name|Type|Required|Description|
|:---|:---|:-------|:----------|
|cluster\_id|string|Yes|Custer ID|

**Special request header \(RequestHead\)**

None. See [Public request headers](intl.en-US/Developer Guide/Make API requests/Common parameters.md#section_mr5_lf1_wdb).

**Request body requestbody**

None.

## Return information {#section_ofh_qnb_wdb .section}

**Response line \(ResponseLine\)**

``` {#codeblock_42l_7e4_5lh}
HTTP/1.1 200 OK
```

**Special response header \(ResponseHead\)**

None. See [Public response headers](intl.en-US/Developer Guide/Make API requests/Common parameters.md#section_zr5_lf1_wdb).

**Response body \(ResponseBody\)**

``` {#codeblock_v0h_2iv_1e0}
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
```

**Response body explanation**

Cluster format

|Name|Type|Description|
|:---|:---|:----------|
|agent\_version|string|The Agent version.|
|cluster\_id|string|The cluster ID, which is the unique identifier of the cluster.|
|created|string|The creation time of the cluster.|
|external\_loadbalancer\_id|string|The Server Load Balancer instance ID of the cluster.|
|master\_url|string|The master address of the cluster, which is used to connect to the cluster to perform operations. For more information, see [Access Kubernetes clusters by using SSH](../../../../intl.en-US//Access a Kubernetes cluster by using SSH.md#).|
|name|string|The cluster name, which is specified when you create the cluster and is unique for each account.|
|network\_mode|string|The network mode of the cluster \(Virtual Private Cloud \(VPC\)\).|
|region\_id|string|The ID of the region where the cluster is located.|
|security\_group\_id|string|The security group ID.|
|size|string|The number of nodes.|
|state|string|The cluster status.|
|updated|string|The last update time.|
|vpc\_id|string|The VPC ID.|
|vswitch\_id|string|The VSwitch ID.|

## Examples {#section_inw_54b_wdb .section}

**Request example**

``` {#codeblock_xqw_cl5_io7}
GET /clusters/C5b5e80b0b64a4bf6939d2d8fbbc5ded7 HTTP/1.1
<Public request header>
```

**Response example**

``` {#codeblock_8al_nyn_hpd}
HTTP/1.1 200 Ok
<Public response header>
{
    "agent_version": "0.5-e56dab3",
    "cluster_id": "c978ca3eaacd3409a9437db07598f****",
    "created": "2015-12-11T03:52:40Z",
    "external_loadbalancer_id": "1518f2b7e4c-cn-beijing-bt****",
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
}
```

