# Delete a cluster {#reference_kbk_hrb_wdb .reference}

This topic describes how to delete a cluster according to the cluster ID and release all node resources.

## Request information {#section_svn_jrb_wdb .section}

**Request line**

```
DELETE /clusters/{cluster_id} HTTP/1.1
```

**Request line parameter \(URI Param\)**

|Name|Type|Required|Description|
|:---|:---|:-------|:----------|
|cluster\_id|String|Yes|Custer ID|

**Special request header**

None. See [Public request headers](reseller.en-US/developer guide/Cluster API call method/Common parameters.md#section_mr5_lf1_wdb).

**Request body**

None.

## Response information {#section_d3s_lrb_wdb .section}

**Response line**

```
HTTP/1.1 202 Accepted
```

**Special response header**

None. See [Public response headers](reseller.en-US/developer guide/Cluster API call method/Common parameters.md#section_zr5_lf1_wdb).

**Response body**

None

## Example {#section_lrr_nrb_wdb .section}

**Request example**

```
DELETE /clusters/Cccfd68c474454665ace07efce924f75f HTTP/1.1
<Public request header>
```

**Response sample**

```
HTTP/1.1 202 Accepted
<Public response header>
```

