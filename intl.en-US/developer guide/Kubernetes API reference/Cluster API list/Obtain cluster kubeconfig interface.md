# Obtain cluster kubeconfig interface {#reference_np5_jsl_xdb .reference}

Return the Kubernetes cluster access kubeconfig that contains the identity information of the current logon user.

## Request information {#section_g5f_xtl_xdb .section}

**Request line \(RequestLine\)**

```
GET /k8s/:clusterid/user_config
```

**Special request header \(RequestHead\)**

None. See [EN-US\_TP\_7274.md\#section\_mr5\_lf1\_wdb](intl.en-US/Developer Guide/Kubernetes API reference/Cluster API call method/Common parameters.md#section_mr5_lf1_wdb).

## Response information {#section_f1s_ztl_xdb .section}

**Response line \(ResponseLine\)**

```
HTTP/1.1 200 OK
```

**Special response header \(ResponseHead\)**

None. See [EN-US\_TP\_7274.md\#section\_zr5\_lf1\_wdb](intl.en-US/Developer Guide/Kubernetes API reference/Cluster API call method/Common parameters.md#section_zr5_lf1_wdb).

**Response body \(ResponseBody\)**

```

    "config": "string"

```

**Response body explanation**

|Name|Type|Description|
|:---|:---|:----------|
|config|string|The cluster access kubeconfig of the current logon user.|

## Example {#section_nmr_n5l_xdb .section}

**Request example**

```
GET /k8s/c5b5e80b0b64a4bf6939d2d8fbbc5ded7/user_config HTTP/1.1
<Public request headers>
```

**Response example**

```
HTTP/1.1 200 Ok
<Public response headers>

     "config": "xxxxxxxx"

```

