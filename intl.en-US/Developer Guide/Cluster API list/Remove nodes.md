# Remove nodes {#reference_hrl_33b_wdb .reference}

This topic describes how to remove worker nodes from a Kubernetes cluster.

**Note:** 

-   Removing nodes causes pod migration. This may affect the services provided by the pods running on the nodes. Therefore, we recommend that you remove nodes only when fewer services are in demand.
-   Removing nodes may cause unintended risks. We recommend that you back up your data in advance and exercise caution when performing this action.
-   When you start to remove nodes, the nodes are set to the unschedulable state.
-   Only Worker nodes can be removed.

## Request information {#section_e2b_mjb_wdb .section}

**Request line \(RequestLine\)**

``` {#codeblock_877_0cf_8g6}
POST /clusters/{cluster_id}/nodes HTTP/1.1
```

**Request line parameter \(URI Param\)**

|Parameter|Type|Required|Description|
|:--------|:---|:-------|:----------|
|cluster\_id|String|Yes|The ID of the cluster from which you want to remove nodes.|

**Special request headers**

None. For more information, see [Public request headers](reseller.en-US/Developer Guide/Make API requests/Common parameters.md#section_mr5_lf1_wdb).

**Request body \(RequestBody\)**

``` {#codeblock_f54_aru_upk}
{
    "nodes": "The nodes that you want to remove",
    "release_node": "Specifies whether to release the Elastic Compute Service (ECS) instances at the same time."
}
```

**Request body explanation**

|Parameter|Type|Required|Description|
|:--------|:---|:-------|:----------|
|Nodes|Array|Yes|The nodes that you want to remove.|
|release\_node|Boolean|No|Specifies whether to release the ECS instances at the same time. By default, the ECS instances are not released.|

## Return information {#section_awy_llb_wdb .section}

**Response line \(ResponseLine\)**

``` {#codeblock_r66_o68_1ak}
HTTP/1.1 202 OK
```

**Special response headers \(ResponseHead\)**

None. For more information, see [Public response headers](reseller.en-US/Developer Guide/Make API requests/Common parameters.md#section_zr5_lf1_wdb).

## Examples {#section_t2q_rlb_wdb .section}

**Request example**

``` {#codeblock_d9b_5og_8iz}
POST /clusters/Cccfd68c474454665ace07efce924****/attach HTTP/1.1
<Public request header>
{
    "password": "Hello1234",
    "nodes": [
        "i-xxxx",
        "i-yyyy"
    ]
}
```

**Response example**

``` {#codeblock_4zg_p9f_qlt}
HTTP/1.1 202 Accepted
<Public response header>
			
```

