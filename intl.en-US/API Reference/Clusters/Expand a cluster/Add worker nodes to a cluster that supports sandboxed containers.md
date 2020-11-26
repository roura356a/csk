# Add worker nodes to a cluster that supports sandboxed containers

You can call ScaleOutCluster to add worker nodes to a cluster that supports sandboxed containers. Worker nodes can be deployed in multiple zones.

## Request information

Request line

```
POST /api/v2/clusters/{cluster_id} HTTP/1.1
```

|Parameter|Type|Required|Description|
|:--------|:---|:-------|:----------|
|cluster\_id|string|Yes|The ID of the cluster.|

Operation-specific request parameters

No operation-specific request parameters are used. For more information, see [Common parameters](/intl.en-US/API Reference/Common parameters.md).

Request body

```
{
    "login_password":"The password for logging on to worker nodes. You must set login_password or key_pair.",
    "worker_instance_charge_type": "PostPaid",
    "vswitch_ids":"The IDs of VSwitches. Specify one to three VSwitch IDs.",
    "worker_instance_types":"The Elastic Compute Service (ECS) instance types of worker nodes.",
    "worker_system_disk_category":"The system disk type of worker nodes.",
    "worker_system_disk_size":"The system disk size of a worker node.",
    "worker_data_disk":"Specifies whether to mount data disks. Valid values: true and false.",
    "worker_data_disk_category":"The data disk type of worker nodes.",
    "worker_data_disk_size":"The size of a data disk that a worker node uses.",
    "tags":"The tags of the cluster. This parameter must be an array.",
    "count":"The number of worker nodes that you want to add."
}
```

|Parameter|Type|Required|Description|
|:--------|:---|:-------|:----------|
|count|int|Yes|The number of worker nodes that you want to add.|
|login\_password|string|Yes|The password for logging on to worker nodes. The password must be 8 to 30 characters in length and contain a minimum of three of the following character types: uppercase letters, lowercase letters, digits, and special characters. You must set login\_password or key\_pair.|
|key\_pair|string|Yes|The name of the key pair. You must set key\_pair or login\_password.|
|vswitch\_ids|list|Yes|The VSwitch IDs of worker nodes.|
|worker\_data\_disk|bool|Yes|Specifies whether to mount data disks to worker nodes. Valid values: -   true: mounts data disks to worker nodes.
-   false: does not mount data disks to worker nodes.

Set the value to true.|
|worker\_data\_disk\_size|string|Yes|The size of a data disk. Unit: GiB. Set the value to 200 or larger.|
|worker\_instance\_types|list|Yes|The ECS instance types of worker nodes that support sandboxed containers. Valid values: -   ecs.ebmg5s.24xlarge
-   ecs.ebmc5s.24xlarge
-   ecs.ebmgn6i.24xlarge |
|worker\_data\_disk\_category|string|No|The type of data disks.|
|worker\_system\_disk\_category|string|No|The system disk type of worker nodes.|
|worker\_system\_disk\_size|int|No|The system disk size of a worker node. Unit: GiB.|

## Response information

Response line

```
HTTP/1.1 202 Accepted
```

Operation-specific response parameters

No operation-specific response parameters are used. For more information, see [Common parameters](/intl.en-US/API Reference/Common parameters.md).

Response body

```
{
    "cluster_id":"string",
    "request_id":"string",
    "task_id":"string"
}
```

## Examples

Sample requests

```
POST /api/v2/clusters/Cccfd68c474454665ace07efce924**** HTTP/1.1
<Common request parameters>
{
    "login_password":"xxxxxxxx",
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

Sample success responses

```
HTTP/1.1 202 Accepted
<Common response parameters>
{
    "cluster_id": "Cccfd68c474454665ace07efce924****",
    "request_id": "687C5BAA-D103-4993-884B-C35E4314A1E1",
    "task_id": "T-5a54309c80282e39ea00002f"
}
```

