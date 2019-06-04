# Create a managed Kubernetes cluster {#reference_af2_kc1_dfb .reference}

This topic describes how to create a managed Kubernetes cluster. You must specify the number of Worker nodes for the cluster.

## Request information {#section_dt4_pl2_xdb .section}

**Request line**

```
POST /clusters HTTP/1.1 
```

**Special request header**

None. See [Public request headers](reseller.en-US/Developer Guide/Cluster API call method/Common parameters.md#section_mr5_lf1_wdb).

**Request body**

```
{
    "disable_rollback": "whether or not to roll back if the cluster fails to be created",
    "name": "cluster name",
    "timeout_mins": "timeout for creating the cluster",
    "cluster_type": "cluster type, Managed Kubernetes",
    "region_id": "region",
    "vpcid": "Virtual Private Cloud (VPC) ID",
    "zoneid": "zone",
    "vswitchid": "VSwitch ID",    
    "container_cidr": "pod Classless Inter-Domain Routing (CIDR)",
    "service_cidr": "service CIDR",
    "cloud_monitor_flags":"whether or not to install the cloud monitoring plug-in",
    "login_password": "password used to log on to the node by using SSH. Use either this parameter or the key_pair.",
    "key_pair":"key pair name. use either this parameter or login_password.",
    "worker_instance_charge_type":"worker node payment type PrePaid|PostPaid",
    "worker_period_unit":"subscription unit, which includes month and year, and takes effect only for the prepaid type.
    "worker_period":"subscription period, which takes effect only for the prepaid type",
    "worker_auto_renew":"worker node auto renew true|false",
    "worker_auto_renew_period":"worker node renew period",
    "worker_instance_type": "instance type of worker nodes",
    "worker_system_disk_category": "system disk type of worker nodes",
    "worker_system_disk_size": "system disk size of worker nodes",
    "worker_data_disk":"whether or not to mount data disks true|false",
    "data_disk_category": "data disk category",
    "worker_data_disk_size":"data disk size",
    "num_of_nodes": "number of worker nodes",
    "snat_entry": "whether or not to configure the SNATEntry",
        "public_slb":"whether or not to create the EIP for the Internet API server",
}
```

**Request body explanation**

|Name|Type|Required|Description|
|----|----|--------|-----------|
|disable\_rollback|bool|No|Whether or not to roll back if the cluster fails to be created: -   true: Not roll back if the cluster fails to be created.
-   false: Roll back if the cluster fails to be created.

 Resources generated in the creation process are released if you select to roll back. We do not recommend that you use false.|
|name|string|Yse|The cluster name, which can contain uppercase English letters, lowercase English letters, Chinese characters, numbers, and hyphens \(-\).|
|timeout\_mins|int|No|The timeout \(in minutes\) for creating the cluster resource stack. The default value is 60.|
|region\_id|string|Yse|The ID of the region in which the cluster resides.|
|zoneid|string|Yse|The zone of the region in which the cluster resides.|
|vpcid|string|No|VPC ID, which can be empty. If left empty, the system automatically creates a VPC. The CIDR block of the automatically created VPC is 192.168.0.0/16. The VPC ID and VSwitch ID must be empty or have a certain value at the same time.|
|vswitchid|string|No|VSwitch ID, which can be empty. If left empty, the system automatically creates a VSwitch. The CIDR block of the automatically created VSwitch is 192.168.0.0/16.|
|container\_cidr|string|No|The pod CIDR block, which cannot conflict with the VPC CIDR block. If you choose to allow the system to automatically create a VPC, the CIDR block 172.16.0.0/16 is used by default.|
|service\_cidr|string|No|The service CIDR block, which cannot conflict with the VPC CIDR block or the pod CIDR block. If you choose to allow the system to automatically create a VPC, the 172.19.0.0/20 CIDR block is used by default.|
|worker\_instance\_charge\_type|string|No|Worker node payment type, which has the following options: -   PrePaid: Subscription
-   PostPaid: Pay-As-You-Go.

 |
|worker\_period\_unit|string|No|Specify a period when you select the prepaid type. You can choose from: -   Week: Weeks are used as the timing units.
-   Month: Months are used as the timing units.

 |
|worker\_period|int|No|Subscription period. It takes effect and becomes required only when the value of worker\_instance\_charge\_type is set to PrePaid. -   For `PeriodUnit=Week`, period values includes: \{“1”, “2”, “3”, “4”\}，
-   For `PeriodUnit=Month`, period values include: \{ “1”, “2”, “3”, “4”, “5”, “6”, “7”, “8”, “9”, “12”, “24”, “36”,”48”,”60”\}

 |
|worker\_auto\_renew|bool|No|Whether or not to enable worker node auto-renew. Optional values are: -   true: Enable auto renew.
-   false: Disable automatic renew.

 |
|worker\_auto\_renew\_period|int|No|Auto renew period. It takes effect and becomes required only when the value of worker\_instance\_charge\_type is set to PrePaid. -   For `PeriodUnit=Week`: \{“1”, “2”, “3”\}，
-   For `PeriodUnit=Month`: \{“1”, “2”, “3”, “6”, “12”\}

 |
|worker\_data\_disk|string|No|Whether or not to amount data disks. Available options: -   true: Mount data disks onto worker nodes
-   false: Not mount data disks onto worker nodes

 |
|worker\_data\_disk\_category|int|No|Data disk type|
|worker\_data\_disk\_size|string|No|Data disk size|
|worker\_instance\_type|string|Yse|The ECS instance type code of the worker nodes. For more information, see[Instance type families](../../../../reseller.en-US/Instances/Instance type families.md#)|
|worker\_system\_disk\_category|string|Yes|The system disk type of the worker nodes.|
|worker\_system\_disk\_size|int|Yes|The system disk size of the worker nodes.|
|login\_password|string|Yes|The SSH logon password, which is 8–30 characters long and must contain uppercase letters/lowercase letters, numbers, and special characters at the same time. Choose either SSH logon password or key\_pair.|
|key\_pair|string|Yes|The keypair name. Use either this parameter or login\_password.|
|num\_of\_nodes|int|Yes|The number of worker nodes. The value range is \[0,300\].|
|snat\_entry|bool|Yes|Whether or not to configure the SNAT for VPC. This parameter must be set to true if a VPC is created automatically. Configure the parameter value according to the outbound capability if you select to use an existing VPC to create the cluster.|
|cloud\_monitor\_flags|bool|No|Whether or not to install the cloud monitoring plug-in.|
|public\_slb|bool|No|Whether or not to enable the Internet API server: -   true: indicates to enable the Internet API server. This is the default value.
-   false: indicates not to create the Internet API server. If you select this value, only the intranet API server will be created.

 |

## Response information {#section_ljv_tm2_xdb .section}

**Response line**

```
HTTP/1.1 202 Accepted
```

**Special response header**

None. See [Public response headers](reseller.en-US/Developer Guide/Cluster API call method/Common parameters.md#section_zr5_lf1_wdb).

**Response body**

```
{
"cluster_id":"string",
"request_id":"string",
"task_id":"string"
}
```

## Examples {#section_hcw_wm2_xdb .section}

**Request example**

```
POST /clusters HTTP/1.1
<Public request header>
{
"disable_rollback": true,
"name": "my-test-Kubernetes-cluster",
"timeout_mins": 60,
"cluster_type": "ManagedKubernetes",
"region_id": "cn-beijing",
"zoneid": "cn-beijing-f",
"vpcid": "",
"vswitchid": "",
"num_of_nodes": 1,
"container_cidr": "172.16.0.0/16",
"service_cidr": "172.19.0.0/20",
"cloud_monitor_flags": true,
"master_instance_type": "ecs.sn1ne.large",
"master_system_disk_category": "cloud_efficiency",
"master_system_disk_size": 40,

"worker_instance_type": "ecs.sn1ne.large",
"worker_system_disk_category": "cloud_efficiency",
"worker_system_disk_size": 40,

"snat_entry": true,
"login_password": "Hello1234"

}
```

**Response examples**

```
HTTP/1.1 202 Accepted
<Public response header>
{
    "cluster_id": "cb95aa626a47740afbf6aa099b650d7ce",
    "request_id": "687C5BAA-D103-4993-884B-C35E4314A1E1",
    "task_id": "T-5a54309c80282e39ea00002f"
}
```

