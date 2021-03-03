# Add worker nodes to a cluster

You can call ScaleOutCluster to add worker nodes to a cluster. Worker nodes in a cluster can be deployed in multiple zones.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=ScaleOutCluster&type=ROA&version=2015-12-15)

## Request parameter types

This operation uses only common request parameters. For more information, see Common parameters.

## Request syntax

```
post /api/v2/clusters/{ClusterId} http|https
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|c82e6987e2961451182edacd74faf\*\*\*\*|The ID of the cluster. |
|count|Integer|No|3|The number of worker nodes that you want to add. |
|worker\_instance\_charge\_type|String|No|PrePaid|The billing method of worker nodes. Valid values:

-   PrePaid: subscription.
-   PostPaid: pay-as-you-go.

Default value: PostPaid. |
|worker\_period|Integer|No|1|The subscription duration of worker nodes. This parameter takes effect and is required only if worker\_instance\_charge\_type is set to PrePaid. If `worker_period_unit` is set to Month, valid values of worker\_period include 1, 2, 3, 6, and 12. |
|worker\_period\_unit|String|No|Month|The unit of the subscription duration. This parameter is required if worker\_instance\_charge\_type is set to PrePaid. A value of Month specifies that the subscription duration is measured in months. |
|worker\_auto\_renew|Boolean|No|true|Specifies whether to enable auto renewal for worker nodes. Valid values:

-   true: enables auto renewal.
-   false: disables auto renewal. |
|worker\_auto\_renew\_period|Integer|No|6|The auto renewal period for worker nodes. This parameter takes effect and is required only if worker\_instance\_charge\_type is set to PrePaid and worker\_auto\_renew is set to true. If `worker_period_unit` is set to Month, valid values of worker\_auto\_renew\_period include 1, 2, 3, 6, and 12. |
|worker\_system\_disk\_category|String|No|cloud\_efficiency|The system disk type of worker nodes. |
|worker\_system\_disk\_size|Integer|No|120|The system disk size of a worker node. Unit: GiB. |
|worker\_data\_disk|Boolean|No|true|Specifies whether to mount data disks to worker nodes. Valid values:

-   true: mounts data disks.
-   false: does not mount data disks. |
|key\_pair|String|No|secrity-key|The name of the key pair. You must set key\_pair or `login_password`. |
|login\_password|String|No|Hello@1234|The SSH logon password. The password must be 8 to 30 characters in length and contain a minimum of three of the following character types: uppercase letters, lowercase letters, digits, and special characters. You must set login\_password or `key_pair`. |
|cloud\_monitor\_flags|Boolean|No|true|Specifies whether to install the Cloud Monitor agent. |
|cpu\_policy|String|No|none|The CPU management policy. For Kubernetes 1.12.6 and later, valid values of cpu\_policy include static and none. Default value: none. |
|disable\_rollback|Boolean|No|true|Specifies whether to retain all resources if the operation fails. |
|image\_id|String|No|""|The ID of the image. By default, a system image is used. You can also specify a custom image to replace the system image. For more information, see .[Use a custom image to create an ACK cluster](/intl.en-US/Best Practices/Migrate a self-built Kubernetes cluster to Container Service for Kubernetes/Use a custom image to create an ACK cluster.md) |
|user\_data|String|No|""|The user data of the node. You can run batch or PowerShell scripts on Windows-based ECS instances. Before you perform Base64 encoding, make sure that the first line includes \[bat\] or \[powershell\]. Linux-based ECS instances support shell scripts. For more information about supported formats, see [User data formats](https://cloudinit.readthedocs.io/en/latest/topics/format.html) and [Prepare user data](/intl.en-US/Instance/Manage instances/User data/Prepare user data.md).

If your script file is larger than 1 KB, we recommend that you upload the script to Object Storage Service \(OSS\) and pull the script through the internal endpoint of the OSS bucket.

You can run scripts to create a cluster or add nodes to a cluster. After requests are sent, you must check whether the execution of scripts succeeds. |
|runtime|Object|No| |The runtime of containers. |
|name|String|No|docker|The name of the container runtime. |
|version|String|No|19.03.5|The version of the container runtime. |
|vswitch\_ids|Array of String|No|vsw-2ze4jvvvade1yk899\*\*\*\*|The IDs of VSwitches. |
|worker\_instance\_types|Array of String|No|ecs.c5.xlarge|The ECS instance types of worker nodes. |
|rds\_instances|Array of String|No|rm-2zev748xi27xc\*\*\*\*|The Relational Database Service \(RDS\) instances. |
|worker\_data\_disks|Array|No| |The data disk configurations of worker nodes. |
|category|String|No|cloud\_ssd|The type of data disks. |
|encrypted|String|No|false|Specifies whether to encrypt data disks. |
|size|String|No|120|The size of a data disk. |
|tags|Array|No| |The tags of the cluster. |
|key|String|No|tier|The key of the tag. |
|value|String|No|frontend|The value of the tag. |
|taints|Array|No| |The taints to be added to the nodes. |
|effect|String|No|NoSchedule|The scheduling policy. |
|key|String|No|node\_type|The key of the taint. |
|value|String|No|prod|The value of the taint. |

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|cluster\_id|String|"c82e6987e2961451182edacd74faf\*\*\*\*"|The ID of the cluster. |
|request\_id|String|"687C5BAA-D103-4993-884B-C35E4314A1E1"|The ID of the request. |
|task\_id|String|"T-5a54309c80282e39ea00002f"|The ID of the task. |

## Examples

Sample requests

```
POST /api/v2/clusters/[ClusterId] HTTP/1.1
Common request parameters
{
    "ClusterId":"c82e6987e2961451182edacd7****",
    "count":1,
    "key_pair":"common",
    "vswitch_ids":[
        "vsw-uf684tfrpwup8gcsw****"
    ],
    "worker_instance_types":[
        "ecs.c5.xlarge"
    ],
    "worker_system_disk_category":"cloud_efficiency",
    "worker_system_disk_size":120,
    "worker_data_disk":false,
    "worker_data_disks":[
        {
            "category":"cloud_ssd",
            "size":500
        }
    ],
    "tags":[
        {
            "key":"tier",
            "value":"frontend"
        }
    ]
}
```

Sample success responses

`XML` format

```
<cluster_id>c82e6987e2961451182edacd74faf****</cluster_id>
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

