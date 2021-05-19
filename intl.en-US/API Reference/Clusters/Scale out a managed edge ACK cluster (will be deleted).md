# Scale out a managed edge ACK cluster \(will be deleted\)

Adds worker nodes to a managed edge Kubernetes cluster. You can add only Edge Node Service \(ENS\) instances to a managed edge Kubernetes cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=CreateCluster&type=ROA&version=2015-12-15)

## Request structure

```
POST /api/v2/clusters/ClusterId HTTP/1.1
Content-Type:application/json
{
  "count" : Long,
  "worker_instance_type" : "String",
  "ens_region_id" : "String",
  "worker_image_id" : "String",
  "ens_internet_charge_type": "String",
  "worker_period": Long,
  "login_password": "String",
  "worker_system_disk_size": Long,
  "is_edge_worker": Boolean,
  "worker_data_disk_size": Long,
  "worker_auto_renew": Boolean,
  "worker_auto_renew_period": Long,
  "timeout_mins": Long
}
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|Cccfd68c474454665ace07efce924\*\*\*\*|The ID of the cluster. |

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|count|Long|Yes|1|The number of ENS instances that you want to add.|
|worker\_instance\_type|String|Yes|ens.sn1.tiny|The type of ENS instance.|
|ens\_region\_id|String|Yes|cn-beijing-cmcc-2|The ID of the region where the ENS instances are deployed.|
|worker\_image\_id|String|Yes|centos\_7\_06\_64\_20G\_alibase\_2019\*\*\*\*|Specifies a custom image for nodes. By default, the system image is used. You can select a custom image to replace the default image. For more information, see [Custom images](~~146647~~). |
|ens\_Internet\_charge\_type|String|Yes|BandwidthByDay|The billing method. Valid values:-   `BandwidthByDay`: Pay by the daily peak bandwidth.
-   `95BandwidthByMonth`: Pay by the monthly 95th percentile bandwidth. |
|worker\_period|Long|Yes|1|The subscription duration of worker nodes. This parameter takes effect and is required only if `worker_instance_charge_type` is set to `PrePaid`.

Valid values: 1, 2, 3, 6, 12, 24, 36, 48, and 60.

Default value: 1. |
|login\_password|String|Yes|HelloWorl\*\*\*\*|The password for Secure Shell \(SSH\) logon. You must set this parameter or the `key_pair` parameter. The password must be 8 to 30 characters in length, and must contain at least three of the following character types: uppercase letters, lowercase letters, digits, and special characters. |
|worker\_system\_disk\_size|Long|Yes|40|The size of the system disk. Unit: GiB. The value must be a multiple of 10 from 20 to 100. The size of the system disk must be greater than that of the operating system image.|
|is\_edge\_worker|Boolean|Yes|true|Specifies whether to add the ENS instances as edge worker nodes. Valid values:-   `true`: adds the ENS instances as edge worker nodes.
-   `false`: does not add the ENS instances as edge worker nodes.

To add ENS instances to a manged edge Kubernetes cluster, the value must be set to `true`.|
|worker\_data\_disk\_size|Long|No|40|The size of the data disk. If no data disk is required, set the value to 0. Otherwise, set the value to a multiple of 10 from 20 to 200.|
|worker\_auto\_renew|Boolean|No|true|Specifies whether to enable auto-renewal. Valid values:-   `true`: enables auto-renewal.
-   `false`: disables auto-renewal.

Default value: `false`.|
|worker\_auto\_renew\_period|Long|No|1|The subscription duration that is added each time the ENS instances are renewed. This parameter is required if `worker_auto_renew` is set to `true`. Valid values: 1 to 12.|
|timeout\_mins|Long|No|60|The timeout period in minutes during which the ENS instances must be added.|

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
|cluster\_id|String|Cccfd68c474454665ace07efce924\*\*\*\*|The ID of the cluster. |
|request\_id|String|687C5BAA-D103-4993-884B-C35E4314A1E1|The ID of the request. |
|task\_id|String|T-5a54309c80282e39ea00002f|The ID of the task. |

## An example on how to scale out a managed edge Kubernetes cluster

Sample requests

```
POST /api/v2/clusters/Cccfd68c474454665ace07efce924**** 
<Common request headers>
{
  "timeout_mins": 60,
  "worker_instance_type": "ens.sn1.tiny",
  "ens_region_id": "cn-beijing-telecom",
  "worker_image_id": "m-2QVLO2T8NYgm8CNQVg****",  
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

`XML` format

```
<cluster_id>Cccfd68c474454665ace07efce924****</cluster_id>
<task_id>T-5a54309c80282e39ea00002f</task_id>
<request_id>687C5BAA-D103-4993-884B-C35E4314A1E1</request_id>
```

`JSON` format

```
{
    "cluster_id": "c82e6987e2961451182edacd74faf****",
    "task_id": "T-5a54309c80282e39ea00002f",
    "request_id": "687C5BAA-D103-4993-884B-C35E4314A1E1"
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

