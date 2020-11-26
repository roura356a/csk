# Add worker nodes to a managed edge cluster

You can call ScaleOutCluster to add worker nodes to a managed edge cluster. You can add only Edge Node Service \(ENS\) instances to a managed edge cluster.

## Request information

Request line

```
POST /api/v2/clusters/{cluster_id} HTTP/1.1
```

|Parameter|Type|Required|Description|
|---------|----|--------|-----------|
|cluster\_id|string|Yes|The ID of the cluster.|

Operation-specific request parameters

No operation-specific request parameters are used. For more information, see [Common parameters](/intl.en-US/API Reference/Common parameters.md).

Request body

```
{
  "timeout_mins": the time-out duration during which cluster scale-out must be completed,
  "worker_instance_type": the ENS instance type of worker nodes,
  "ens_region_id": ens region id,
  "worker_image_id": the operating system image used by worker nodes,  
  "ens_internet_charge_type": the billing method of worker nodes,
  "worker_period": the subscription duration of worker nodes,
  "worker_auto_renew": specifies whether to enable auto renewal for worker nodes,
  "worker_auto_renew_period": the auto renewal duration of worker nodes,
  "login_password": the password for logging on to worker nodes that you want to add,
  "count": the number of worker nodes that you want to add,
  "worker_system_disk_size": the system disk size of a worker node,
  "worker_data_disk_size": the size of a data disk,
  "is_edge_worker": specifies whether to configure the worker node as an edge node,
}
```

|Parameter|Type|Required|Description|
|---------|----|--------|-----------|
|count|int|Yes|The number of worker nodes that you want to add.|
|worker\_instance\_type|string|Yes|The ENS instance type of worker nodes.|
|ens\_region\_id|string|Yes|The region ID of the ENS instances.|
|worker\_image\_id|string|Yes|The operating system image used by worker nodes.|
|ens\_internet\_charge\_type|string|Yes|The billing method of the ENS instances. This parameter is required if you purchase ENS instances for the first time. If you have existing ENS instances, the current billing method is used. Valid values: -   BandwidthByDay: Pay by the daily peak bandwidth.
-   95BandwidthByMonth: Pay by the monthly 95th percentile bandwidth. |
|worker\_period|int|Yes|The subscription duration of worker nodes. Unit: months. Valid values: 1, 2, 3, 4, 5, 6, 7, 8, 9, and 12.|
|login\_password|string|Yes|The password for logging on to worker nodes.|
|worker\_system\_disk\_size|int|Yes|The system disk size of a worker node. Unit: GiB. The value must be a multiple of 10 from 20 to 100. The system disk size must be greater than the size of the operating system image.|
|is\_edge\_worker|bool|Yes|Set the value to true.|
|worker\_data\_disk\_size|int|No|The size of a data disk. If data disks are not required, set the value to 0. If data disks are required, set the value to a multiple of 10 from 20 to 200.|
|worker\_auto\_renew|bool|No|Specifies whether to enable auto renewal for worker nodes. Valid values: -   true: enables auto renewal.
-   false: disables auto renewal.

Default value: false. |
|worker\_auto\_renew\_period|int|No|The auto renewal period of worker nodes. This parameter is required if worker\_auto\_renew is set to true. Valid values: 1 to 12.|
|timeout\_mins|int|No|The time-out duration.|

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
  "timeout_mins": 60,
  "worker_instance_type": "ens.sn1.tiny",
  "ens_region_id": "cn-beijing-telecom",
  "worker_image_id": "m-2QVLO2T8NYgm8CNQVg15gF",  
  "ens_internet_charge_type": "BandwidthByDay",
  "worker_period": 1,
  "worker_auto_renew": true,
  "worker_auto_renew_period": 1,
  "login_password": "Hello1234!",
  "count": 1,
  "worker_system_disk_size": 20,
  "worker_data_disk_size": 0,
  "is_edge_worker": true
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

