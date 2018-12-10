# Download the cluster certificate {#reference_z1q_fsl_xdb .reference}

This topic describes how to download the certificate used by the currently logged on user to access the cluster.

## Request information {#section_vnh_nsl_xdb .section}

**Request line**

```
GET /clusters/:clusterid/certs
```

**Special request header**

None. See [Public request headers](reseller.en-US/Developer Guide/Cluster API call method/Common parameters.md#section_mr5_lf1_wdb).

## Response information {#section_yfc_psl_xdb .section}

**Response line**

```
HTTP/1.1 200 OK
```

**Special response header**

None. See [Public response headers](reseller.en-US/Developer Guide/Cluster API call method/Common parameters.md#section_zr5_lf1_wdb).

**Response body**

```
{
    "config": "string"
}
```

**Response body description**

|Name|Type|Description|
|:---|:---|:----------|
|config|String|Kubeconfig file used by the current user to access the cluster|

## Examples {#section_f22_zsl_xdb .section}

**Request example**

```
GET /clusters/C5b5e80b0b64a4bf6939d2d8fbbc5ded7/certs HTTP/1.1
<Public request header>
```

**Response example**

```
HTTP/1.1 200 Ok
<Public response header>
{
     "config": "xxxxxxxx"
}
```

