# Scale out an ACK cluster \(will be deleted\)

Adds worker nodes to a Container Service for Kubernetes \(ACK\) cluster. The worker nodes can be deployed in multiple zones.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=ScaleOutCluster&type=ROA&version=2015-12-15)

## Request structure

```
POST /api/v2/clusters/ClusterId HTTP/1.1 
Content-Type:application/json
{
  "count" : Long,
  "key_pair" : "String",
  "login_password" : "String",
  "vswitch_ids" : [ "String" ],
  "worker_instance_charge_type" : "String",
  "worker_period" : Long,
  "worker_period_unit" : "String",
  "worker_auto_renew" : Boolean,
  "worker_auto_renew_period" : Long,
  "worker_instance_types" : [ "String" ],
  "worker_system_disk_category" : "String",
  "worker_system_disk_size" : Long,
  "worker_data_disks" : [ {
    "category" : "String",
    "size" : Long,
    "encrypted" : "String",
    "auto_snapshot_policy_id" : "String"
  } ],
  "cloud_monitor_flags" : Boolean,
  "cpu_policy" : "String",
  "image_id" : "String",
  "user_data" : "String",
  "rds_instances" : [ "String" ],
  "tags" : [ {
    "key" : "String",
    "value" : "String"
  } ],
  "taints" : [ {
    "key" : "String",
    "value" : "String",
    "effect" : "String"
  } ],
  "runtime" : {
    "name" : "String",
    "version" : "String"
  }
}
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|c82e6987e2961451182edacd74faf\*\*\*\*|The ID of the cluster. |

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|count|Long|Yes|3|The number of worker nodes that you want to add. |
|key\_pair|String|Yes|secrity-key|The name of the key pair. You must set this parameter or the `login_password` parameter. |
|login\_password|String|Yes|Hello@1234|The password for Secure Shell \(SSH\) logon. You must set this parameter or the `key_pair` parameter. The password must be 8 to 30 characters in length, and must contain at least three of the following character types: uppercase letters, lowercase letters, digits, and special characters. |
|vswitch\_ids|Array of String|Yes|vsw-2ze4jvvvade1yk899\*\*\*\*|The IDs of the vSwitches that are specified for the nodes that you want to add. Before you scale out an ACK cluster, the cluster uses the vSwitches that are specified when the cluster is created. |
|worker\_instance\_charge\_type|String|No|PrePaid|The billing method of worker nodes. Valid values:

-   `PrePaid`: subscription.
-   `PostPaid`: pay-as-you-go.

Default value: `PostPaid`. |
|worker\_period|Long|No|1|The subscription duration of worker nodes. This parameter takes effect and is required only if `worker_instance_charge_type` is set to `PrePaid`.

Valid values: 1, 2, 3, 6, 12, 24, 36, 48, and 60.

Default value: 1. |
|worker\_period\_unit|String|No|Month|The billing cycle of worker nodes. This parameter is required only if worker\_instance\_charge\_type is set to `PrePaid`.

Set the value to `Month`. Worker nodes are billed only on a monthly basis. |
|worker\_auto\_renew|Boolean|No|true|Specifies whether to enable auto-renewal for worker nodes. This parameter takes effect only if `worker_instance_charge_type` is set to `PrePaid`. Valid values:

-   `true`: enables auto-renewal.
-   `false`: disables auto-renewal.

Default value: `true`. |
|worker\_auto\_renew\_period|Long|No|6|The cycle of auto-renewal. This parameter take effects and is required only if the subscription billing method is selected for worker nodes.

Valid values: 1, 2, 3, 6, and 12.

Default value: `1`. |
|worker\_instance\_types|Array of String|Yes|ecs.c5.xlarge|The instance types of worker nodes. You must specify at least one instance type. For more information, see [Instance families](~~25378~~).

**Note:** The instance types are listed in descending order of priority. If worker nodes fail to be created based on the instance type of the highest priority, the system attempts to create worker nodes by using the instance type of the next highest priority. |
|worker\_system\_disk\_category|String|Yes|cloud\_efficiency|The type of system disk that is specified for worker nodes. Valid values:

-   `cloud_efficiency`: ultra disk.
-   `cloud_ssd`: standard SSD.
-   `cloud_essd`: enhanced SSD.

Default value: `cloud_ssd`. |
|worker\_system\_disk\_size|Long|Yes|120|The size of the system disk that is specified for worker nodes. Unit: GiB.

Valid values: 40 to 500.

Default value: `120`. |
|worker\_data\_disks|Array of [data\_disk](/intl.en-US/API Reference/Commonly used parameters.md)|No| |The configurations of data disks that are mounted to worker nodes. Each configuration includes disk type and disk size. |
|cloud\_monitor\_flags|Boolean|No|true|Specifies whether to install the CloudMonitor agent. Valid values:

-   `true`: installs the CloudMonitor agent.
-   `false`: does not install the CloudMonitor agent.

Default value: `false`. |
|cpu\_policy|String|No|none|The CPU policy. The following policies are supported if the cluster version is 1.12.6 or later.

-   `static`: This policy allows pods with specific resource characteristics on the node to be granted with enhanced CPU affinity and exclusivity.
-   `none`: This policy indicates that the default CPU affinity is used.

Default value: `none`. |
|image\_id|String|No|m-bp16z7xko3vvv8gt\*\*\*\*|Specifies a custom image for nodes. By default, the system image is used. You can select a custom image to replace the default image. For more information, see [Custom images](~~146647~~). |
|user\_data|String|No|IyEvdXNyL2Jpbi9iYXNoCmVjaG8gIkhlbGxvIEFD\*\*\*\*|The user-defined data for nodes. For more information, see [Prepare user-defined data](https://help.aliyun.com/document_detail/~~49121~~). |
|rds\_instances|Array of String|No|rm-2zev748xi27xc\*\*\*\*|The names of ApsaraDB RDS instances. |
|tags|Array of [tag](/intl.en-US/API Reference/Commonly used parameters.md)|No| |The labels to be added to nodes. Labeling rules:

-   Each label consists of a case-sensitive key-value pair. You can add up to 20 labels.
-   When you add a label, you must specify a unique key but you can leave the value empty. A key cannot exceed 64 characters in length and a value cannot exceed 128 characters in length. Keys and values cannot start with aliyun, acs:, https://, or http://. For more information, see [Labels and Selectors](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set). |
|taints|Array of [taint](/intl.en-US/API Reference/Commonly used parameters.md)|No| |The taints to be added to nodes. Taints are added to nodes to prevent pods from being scheduled to inappropriate nodes. However, toleration rules allow pods to be scheduled to nodes with matching taints. For more information, see [taint-and-toleration](https://kubernetes.io/zh/docs/concepts/scheduling-eviction/taint-and-toleration/). |
|runtime|[runtime](/intl.en-US/API Reference/Commonly used parameters.md)|No| |The container runtime. |

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
|cluster\_id|String|c82e6987e2961451182edacd74faf\*\*\*\*|The ID of the cluster. |
|request\_id|String|687C5BAA-D103-4993-884B-C35E4314A1E1|The ID of the request. |
|task\_id|String|T-5a54309c80282e39ea00002f|The ID of the task. |

## Examples

Sample requests

```
POST /api/v2/clusters/c82e6987e2961451182edacd74faf**** HTTP/1.1 
Content-Type:application/json
{
  "count" : 3,
  "key_pair" : "secrity-key",
  "login_password" : "Hello@1234",
  "vswitch_ids" : [ "vsw-2ze4jvvvade1yk899****" ],
  "worker_instance_charge_type" : "PrePaid",
  "worker_period" : 1,
  "worker_period_unit" : "Month",
  "worker_auto_renew" : true,
  "worker_auto_renew_period" : 6,
  "worker_instance_types" : [ "ecs.c5.xlarge" ],
  "worker_system_disk_category" : "cloud_efficiency",
  "worker_system_disk_size" : 120,
  "worker_data_disks" : [ {
    "category" : "cloud_ssd",
    "size" : 40,
    "encrypted" : "true",
    "auto_snapshot_policy_id" : "sp-2zej1nogjvovnz4z****"
  } ],
  "cloud_monitor_flags" : true,
  "cpu_policy" : "none",
  "image_id" : "m-bp16z7xko3vvv8gt****",
  "user_data" : "IyEvdXNyL2Jpbi9iYXNoCmVjaG8gIkhlbGxvIEFD****",
  "rds_instances" : [ "rm-2zev748xi27xc****" ],
  "tags" : [ {
    "key" : "env",
    "value" : "prod"
  } ],
  "taints" : [ {
    "key" : "key",
    "value" : "value",
    "effect" : "NoSchedule"
  } ],
  "runtime" : {
    "name" : "docker",
    "version" : "19.03.5"
  }
}
```

Sample success responses

`XML` format

```
HTTP/1.1 200 OK
Content-Type:application/xml

<cluster_id>c82e6987e2961451182edacd74faf****</cluster_id>
<request_id>687C5BAA-D103-4993-884B-C35E4314A1E1</request_id>
<task_id>T-5a54309c80282e39ea00002f</task_id>
```

`JSON` format

```
HTTP/1.1 200 OK
Content-Type:application/json

{
  "cluster_id" : "c82e6987e2961451182edacd74faf****",
  "request_id" : "687C5BAA-D103-4993-884B-C35E4314A1E1",
  "task_id" : "T-5a54309c80282e39ea00002f"
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

