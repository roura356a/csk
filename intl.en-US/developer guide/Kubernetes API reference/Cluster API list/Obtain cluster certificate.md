# Obtain cluster certificate {#reference_z1q_fsl_xdb .reference}

Download the certificate used by the current logon user to access the cluster.

## Request information {#section_vnh_nsl_xdb .section}

**Request line \(RequestLine\)**

```
GET /clusters/:clusterid/certs
```

**Special request header**

None. See [EN-US\_TP\_15827.md\#section\_mr5\_lf1\_wdb](intl.en-US/developer guide/Kubernetes API reference/Cluster API call method/Common parameters.md#section_mr5_lf1_wdb).

## Return information {#section_yfc_psl_xdb .section}

**Response line \(ResponseLine\)**

```
HTTP/1.1 200 OK
```

**Special response header \(ResponseHead\)**

None. See [EN-US\_TP\_15827.md\#section\_zr5\_lf1\_wdb](intl.en-US/developer guide/Kubernetes API reference/Cluster API call method/Common parameters.md#section_zr5_lf1_wdb).

**Response body \(ResponseBody\)**

```
{
    "ca": "string",
    "cert": "string",
    "key": "string"
}
```

**Response body explanation**

|Name|Type|Description|
|:---|:---|:----------|
|ca|string|The Certification Authority \(CA\) which issues the certificate for the current logon user to access the cluster.|
|cert|string|The certificate used by the current logon user to access the cluster.|
|key|string|The private key used by the current logon user to access the cluster.|

## Examples {#section_f22_zsl_xdb .section}

**Request example**

```
GET /clusters/C5b5e80b0b64a4bf6939d2d8fbbc5ded7/certs HTTP/1.1
<Public request header>
```

**Response example**

```
HTTP/1.1 200 OK
<Public response header>
{
    "ca": "xxxxxxxx",
    "cert": "xxxxxxxx",
    "key": "xxxxxxxxxx"
}
```

