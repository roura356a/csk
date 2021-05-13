# Scale out a Kubernetes cluster that supports sandboxed containers

Adds worker nodes to a Container Service for Kubernetes \(ACK\) cluster that supports sandboxed containers. The worker nodes can be deployed in multiple zones.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=CreateCluster&type=ROA&version=2015-12-15)

## Request structure

```
POST /api/v2/clusters/ClusterId HTTP/1.1
Content-Type:application/json
{
  "count" : Long,
  "login_password": "String",
  "key_pair" : "String",
  "vswitch_ids" : "[ String ]",
  "worker_data_disk": Boolean,
  "worker_data_disk_size": Long,
  "worker_instance_types": "[ String ]",
  "worker_data_disk_category" : "String",
  "worker_system_disk_category" : "String",
  "worker_system_disk_size": Long
} 
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|Cccfd68c474454665ace07efce924\*\*\*\*|The ID of the cluster. |

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|count|Long|Yes|1|The number of worker nodes that you want to add.|
|login\_password|String|Yes|Hello@12\*\*\*\*|The password for Secure Shell \(SSH\). You must set this parameter or the `key_pair` parameter. The password must be 8 to 30 characters in length, and must contain at least three of the following character types: uppercase letters, lowercase letters, digits, and special characters.|
|key\_pair|String|Yes|secrity-key|The name of the key pair. You must set this parameter or the `login_password` parameter.|
|vswitch\_ids|Array of String|Yes|vsw-2ze48rkq464rsdts1\*\*\*\*"|The IDs of the vSwitches that are specified for the worker nodes.|
|worker\_data\_disk|Boolean|Yes|true|Specify whether to mount a data disk to the worker nodes. Valid values:-   `true`: mounts a data disk to the worker nodes.
-   `false`: does not mount a data disk to the worker nodes.

 To scale out an ACK cluster that supports sandboxed containers, set the value to `true`.|
|worker\_data\_disk\_size|String|Yes|200|The size of the data disk. Unit: GiB. **Note:** Set the value to 200 or larger for ACK clusters that support sandboxed containers. |
|worker\_instance\_types|Array of String|Yes|ecs.ebmg5s.24xlarge|The instance types of the worker nodes that support sandboxed containers. Valid values:-   ecs.ebmg5s.24xlarge
-   ecs.ebmc5s.24xlarge\*
-   ecs.ebmgn6i.24xlarge |
|worker\_data\_disk\_category|String|No|cloud\_efficiency|The type of data disk that is mounted to the worker nodes. Valid values:-   `cloud_efficiency`: ultra disk.
-   `cloud_ssd`: standard SSD.
-   `cloud`: basic disk.

Default value: `cloud_efficiency`.|
|worker\_system\_disk\_category|String|No|cloud\_efficiency|The type of system disk that is specified for the worker nodes. Valid values:-   `cloud_efficiency`: ultra disk.
-   `cloud_ssd`: standard SSD.

Default value: `cloud_ssd`. |
|worker\_system\_disk\_size|Long|No|120|The size of the system disk that is specified for the worker nodes. Unit: GiB. Valid values: 20 to 500. The value of this parameter must be at least 20 and greater than or equal to the size of the image.

Default value: 40 or the size of the image, whichever is greater. |

## Response structure

```
HTTP/1.1 200
Content-Type:application/json
{
  "cluster_id" : "String",
  "request_id" : "String",
  "task_id" : "String"
}
```

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|cluster\_id|String|Cccfd68c474454665ace07efce924\*\*\*\*|The ID of the cluster.|
|request\_id|String|687C5BAA-D103-4993-884B-C35E4314A1E1|The ID of the request.|
|task\_id|String|T-5a54309c80282e39ea00002f|The ID of the task.|

## An example on how to scale out an ACK cluster that supports sandboxed containers

Sample requests

```
POST /api/v2/clusters/Cccfd68c474454665ace07efce924**** 
<Common request headers>
{
    "login_password":"Hello@12****",
    "worker_instance_charge_type":"PostPaid",
    "vswitch_ids":["vsw-2zes3rfz7bmk0nxxxxxxx"],
    "worker_instance_types":["ecs.ebmg5s.24xlarge"],
    "worker_system_disk_category":"cloud_efficiency",
    "worker_system_disk_size":120,
    "worker_data_disk":true,
    "worker_data_disk_category":"cloud_efficiency",
    "worker_data_disk_size":200,
    "tags":[],
    "count":1,
    "disable_rollback":false
}
```

Sample responses

```
HTTP/1.1 202 Accepted
<Common response headers>
{
    "cluster_id": "Cccfd68c474454665ace07efce924****",
    "request_id": "687C5BAA-D103-4993-884B-C35E4314A1E1",
    "task_id": "T-5a54309c80282e39ea00002f"
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

