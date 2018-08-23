# Delete a cluster {#reference_kbk_hrb_wdb .reference}

Delete a cluster according to the cluster ID, and release all node resources of the cluster.

## Request information {#section_svn_jrb_wdb .section}

**Request line \(RequestLine\)**

```
DELETE /clusters/{cluster_id} HTTP/1.1
```

**Request line parameter \(URI Param\)**

|Name|Type|Required|Description|
|:---|:---|:-------|:----------|
|cluster\_id|string|Yes| The cluster ID.|

**Special request header \(RequestHead\)**

None. See [EN-US\_TP\_7274.md\#section\_mr5\_lf1\_wdb](intl.en-US/Developer Guide/Kubernetes API reference/Cluster API call method/Common parameters.md#section_mr5_lf1_wdb).

**Request body \(RequestBody\)**

None.

## Response information {#section_d3s_lrb_wdb .section}

**Response line \(ResponseLine\)**

```
HTTP/1.1 202 Accepted
```

**Special response header \(ResponseHead\)**

None. See [EN-US\_TP\_7274.md\#section\_zr5\_lf1\_wdb](intl.en-US/Developer Guide/Kubernetes API reference/Cluster API call method/Common parameters.md#section_zr5_lf1_wdb).

**Response body \(ResponseBody\)**

None.

## Example {#section_lrr_nrb_wdb .section}

**Request example**

```
DELETE /clusters/Cccfd68c474454665ace07efce924f75f HTTP/1.1
<Public request headers>
```

**Response example**

```
HTTP/1.1 202 Accepted
<Public response headers>
```

