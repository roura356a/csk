# Add existing ECS instances to a cluster {#reference_hrl_33b_wdb .reference}

Add existing Elastic Compute Service \(ECS\) instances to a cluster.

**Note:** The system disk is replaced in the process of adding ECS instances. Therefore, back up the data in advance.

## Request information {#section_e2b_mjb_wdb .section}

**Request line \(RequestLine\)**

```
POST /clusters/{cluster_id}/attach HTTP/1.1
```

**Request line parameter \(URI Param\)**

|Name|Type|Required|Description|
|:---|:---|:-------|:----------|
|cluster\_id|string|Yes|The cluster ID.|

**Special request header \(RequestHead\)**

None. See [EN-US\_TP\_7274.md\#section\_mr5\_lf1\_wdb](intl.en-US/Developer Guide/Kubernetes API reference/Cluster API call method/Common parameters.md#section_mr5_lf1_wdb).

**Request body \(RequestBody\)**

```

    "password": "password of the root account that is used to log on to the ECS instance",
    "instances": "the array of instances to be added"

```

**Request body explanation**

|Name|Type|Required|Description|
|:---|:---|:-------|:----------|
|password|string|Yes|The password of the ECS instance, which is 8–30 characters long and must contain uppercase letters/lowercase letters, numbers, and special characters at the same time.|
|instances|array|Yes|The array of existing ECS instances.|

## Response information {#section_awy_llb_wdb .section}

**Response line \(ResponseLine\)**

```
HTTP/1.1 202 OK
```

**Special response header \(ResponseHead\)**

None. See [EN-US\_TP\_7274.md\#section\_zr5\_lf1\_wdb](intl.en-US/Developer Guide/Kubernetes API reference/Cluster API call method/Common parameters.md#section_zr5_lf1_wdb).

**Response body \(ResponseBody\)**

```

    "list": [
        
            "code": "200",
            "instanceId": "i-2zee3oiwcyoz7kwdo8bt",
            "message": "successful"
        
        
            "code": "200",
            "instanceId": "i-2ze0lgm3y6iylcbtcypf",
            "message": "successful"
        
    
    "task_id": "T-5a544aff80282e39ea000039"

```

## Example {#section_t2q_rlb_wdb .section}

**Request example**

```
POST /clusters/Cccfd68c474454665ace07efce924f75f/attach HTTP/1.1
<Public request headers>

    "password": "Hello1234",
    "instances": [
        "i-xxxx",
        "i-yyyy"
    

```

**Response example**

```
HTTP/1.1 202 Accepted
<Public response headers>

    "list": [
        
            "code": "200",
            "instanceId": "i-xxxx",
            "message": "successful"
        
        
            "code": "200",
            "instanceId": "i-yyyy",
            "message": "successful"
        
    
    "task_id": "T-5a544aff80282e39ea000039"

```

