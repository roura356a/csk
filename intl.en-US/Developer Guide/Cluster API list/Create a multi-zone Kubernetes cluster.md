# Create a multi-zone Kubernetes cluster {#reference_rly_znl_2fb .reference}

This topic describes how to create a highly available Kubernetes cluster across multiple zones, and create a specified number of nodes.

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
"disable_rollback": "indicates whether to roll back if the cluster creation fails",
"name": "cluster name",
"timeout_mins": "cluster creation timeout",
"cluster_type": "Kubernetes",
"region_id": "region",
"multi_az": true,
"vpcid": "VPC ID ",
"container_cidr": "container Classless Inter-Domain Routing (CIDR)", 
"service_cidr": "service CIDR",
"vswitch_id_a": "ID of the switch in the first zone",
"vswitch_id_b": "ID of the switch in the second zone",
"vswitch_id_c": "ID of the switch in the third zone", 
"master_instance_type_a": "instance specification of the Master node in the first zone",
"master_instance_type_b": "instance specification of the Master node in the second zone",
"master_instance_type_c": "instance specification of the Master node in the third zone", 
"master_instance_charge_type":"Master instance payment type that includes postpaid and prepaid",
"master_period_unit":"subscription unit that includes month and year, and takes effect only for the prepaid payment type",
"master_period":"subscription period that takes effect only for the prepaid payment type",
"master_auto_renew":"whether Master nodes auto renew",
"master_auto_renew_period":"Master node renew period", 
"master_system_disk_category": "Master node system disk type",
"master_system_disk_size": "Master node system disk size", 
"master_data_disk":"Whether data disks are mounted to the Master node",
"master_data_disk_category":"Master node data disk type",
"master_data_disk_size":"Master node data disk size", 
"worker_instance_type_a": "instance specification of the Worker node in the first zone",
"worker_instance_type_b": "instance specification of the Worker node in the second zone",
"worker_instance_type_c": "instance specification of the Worker node in the third zone",
"worker_instance_charge_type":"Worker node payment type that includes postpaid and prepaid",
"worker_period_unit":"subscription unit that includes month and year, and takes effect only for the prepaid payment type",
"worker_period":"subscription period that takes effect only for the prepaid payment type",
"worker_auto_renew":"whether Worker nodes auto renew. This parameter can be set to true or false."
"worker_auto_renew_period":"Worker node renew period", 
"worker_system_disk_category": "Worker node system disk type",
"worker_system_disk_size": "Worker node system disk size",
"worker_data_disk":"whether the worker node has data disks mounted",
"worker_system_disk_category": "Whether data disks are mounted to the Worker node",
"worker_system_disk_size": "Worker node data disk size", 
"num_of_nodes_a": "number of the Worker nodes in the first zone",
"num_of_nodes_b": "number of the Worker nodes in the second zone",
"num_of_nodes_c": "number of the Worker nodes in the third zone",
"ssh_flags": "whether enable SSH access over the Internet",
"login_password": "SSH logon password", 
"cloud_monitor_flags":"whether to install the cloud monitoring plugin",
"public_slb":"whether to create the SLB corresponding to the Internet API server"
}
```

**Request body description**

|Name|Type|Required|Description|
|----|----|--------|-----------|
|disable\_rollback|bool|No|Whether to roll back if the cluster creation fails:-   true that indicates not to roll back if the cluster creation fails.
-   false that indicates to roll back if the cluster creation fails.

The default is true. If you select to roll back upon the cluster creation failure, resources generated in the creation process will be released. We recommend that you do not select false.|
|name|string|Yes|cluster name. A cluster name can contain uppercase and lowercase letters, Chinese characters, numbers, and hyphens \(-\).|
|timeout\_mins|int|Yes|timeout \(in minutes\) for creating the cluster resource stack. The default is 60.|
|cluster\_type|string|Yes|cluster type that is fixed to Kubernetes|
|region\_id|string|Yes|ID of the region in which the cluster resides|
|multi\_az|bool|Yes|highly available cluster that is fixed to true|
|vpcid|string|Yes|VPC ID|
|container\_cidr|string|No|container CIDR block that cannot overlap with the VPC CIDR block If you choose to enable the system to automatically create a VPC, the 172.16.0.0/16 CIDR block is used by default.|
|service\_cidr|string|No|service CIDR block that cannot overlap with the VPC CIDR block or the container CIDR block. If you choose to enable the system to automatically create a VPC, the 172.19.0.0/20 CIDR block is used by default.|
|vswitch\_id\_a|string|Yes|ID of the first switch|
|vswitch\_id\_b|string|Yes|ID of the second switch|
|vswitch\_id\_c|string|Yes|ID of the third switch|
|master\_instance\_charge\_type|string|Yes|Master node payment type. Available values are:-   PrePaid, namely, Subscription
-   PostPaid, namely, Pay-As-You-Go. The default is Postpaid.

|
|master\_period\_unit|string|No|period unit of Subscription. You need to set this parameter if you specify the PrePaid payment type. Available values include:-   week, namely, one week is used as the timing unit.
-   month, namely, one month is used as the timing unit.

|
|master\_period|int|No|Subscription period. This parameter setting takes effect and becomes required only if the value of master\_instance\_charge\_type is set to PrePaid. Available values of this parameter for different period unit settings are as follows:-   \{“1”, “2”, “3”, “4”\} for the `PeriodUnit=Week` setting.
-   \{ “1”, “2”, “3”, “4”, “5”, “6”, “7”, “8”, “9”, “12”, “24”, “36”,”48”,”60”\} for the`PeriodUnit=Month` setting.

|
|master\_auto\_renew|bool|No|whether enable Master node automatic renew. Available values are:-   true, namely, enable automatic renew
-   false, namely, disable automatic renew

|
|master\_auto\_renew\_period|int|No|automatic renew period. This parameter setting takes effect and becomes required only if the value of master\_instance\_charge\_type is set to PrePaid. Available values of this parameter for different period unit settings are as follows:-   \{“1”, “2”, “3”\} for the `PeriodUnit=Week` setting.
-   \{“1”, “2”, “3”, “6”, “12”\} for `PeriodUnit=Month`.

|
|master\_instance\_type\_a|string|Yes|specification type code of ECS used by the Master node in the first zone For more information, see [Instance type families](../../../../../reseller.en-US/Product Introduction/Instance type families.md#).|
|master\_instance\_type\_b|string|Yes|specification type code of ECS used by the Master node in the second zone For more information, see [Instance type families](../../../../../reseller.en-US/Product Introduction/Instance type families.md#).|
|master\_instance\_type\_c|string|Yes|specification type code of ECS used by the Master node in the third zone For more information, see [Instance type families](../../../../../reseller.en-US/Product Introduction/Instance type families.md#).|
|master\_system\_disk\_category|string|Yes|Master node system disk type. Available values include:-   cloud\_efficiency, namely, the Ultra cloud disk
-   cloud\_ssd, namely, the SSD cloud disk

|
|master\_system\_disk\_size|int|Yes|Master node system disk size, in GiB|
|master\_data\_disk|bool|No|whether to mount data disks to the Master node. Available values are:-   true, namely, mount data disks
-   false, namely, not mount data disks

|
|master\_data\_disk\_category|string|No|Master node data disk type. This parameter setting takes effect only if data disks are mounted to the Master node. Available values are:-   cloud, namely, basic cloud disks
-   cloud\_efficiency, namely, Ultra cloud disks
-   cloud\_ssd, namely, SSD cloud disks

|
|master\_data\_disk\_size|int|No|Master node data disk size in GiB. This parameter setting takes effect only if data disks are mounted to the Master node.|
|worker\_instance\_charge\_type|string|No|Worker node payment type. The default is Pay-As-You-Go. Available values are:-   PrePaid, namely, Subscription
-   PostPaid, namely, Pay-As-You-Go

|
|worker\_period\_unit|string|No|period unit of Subscription. You need to set this parameter if you specify the PrePaid payment type. Available values include:-   Week, namely, one week is used as the timing unit
-   Month, namely, one month is used as the timing unit

|
|worker\_period|int|No|Subscription period. It takes effect and becomes required only when the value of worker\_instance\_charge\_type is set to PrePaid.-   \{“1”, “2”, “3”, “4”\} for the `PeriodUnit=Week` setting.
-   \{ “1”, “2”, “3”, “4”, “5”, “6”, “7”, “8”, “9”, “12”, “24”, “36”,”48”,”60”\} for the`PeriodUnit=Month` setting.

|
|worker\_auto\_renew|bool|No|whether to enable Worker node automatic renew. Available values include:-   true, namely, enable automatic renew
-   false, namely, disable automatic renew

|
|worker\_auto\_renew\_period|int|No|automatic renew period. This parameter setting takes effect and becomes required only if you select Subscription and enable automatic renew. Available values of this parameter for different period unit settings are as follows:-   \{“1”, “2”, “3”\} for the `PeriodUnit=Week` setting
-   \{“1”, “2”, “3”, “6”, “12”\} for the `PeriodUnit=Month` setting

|
|worker\_instance\_type\_a|string|Yes|specification type code of ECS used by the Worker node in the first zone For more information, see[Instance type families](../../../../../reseller.en-US/Product Introduction/Instance type families.md#)|
|worker\_instance\_type\_b|string|Yes|specification type code of ECS used by the Worker node in the second zone For more information, see[Instance type families](../../../../../reseller.en-US/Product Introduction/Instance type families.md#)|
|worker\_instance\_type\_c|string|Yes|specification type code of ECS used by the Worker node in the third zone For more information, see[Instance type families](../../../../../reseller.en-US/Product Introduction/Instance type families.md#)|
|worker\_system\_disk\_category|string|Yes|Worker node system disk type. Available values include:-   cloud\_efficiency, namely, the Ultra cloud disk
-   cloud\_ssd, namely, the SSD cloud disk

|
|worker\_system\_disk\_size|int|Yes|Worker node system disk size, in GiB|
|worker\_data\_disk|string|No|whether to mount data disks to the Worker node. Available values are:-   true, namely, mount data disks to the Worker node
-   false, namely, not mount data disks to the Worker node

|
|worker\_data\_disk\_category|int|No|Worker node data disk type. This parameter setting takes effect only if data disks are mounted to the Worker node. Available values are:-   cloud, namely, basic cloud disks
-   cloud\_efficiency, namely, Ultra cloud disks
-   cloud\_ssd, namely, SSD cloud disks

|
|worker\_data\_disk\_size|string|No|Worker node data disk size in GiB. This parameter setting takes effect only if data disks are mounted to the Worker node.|
|num\_of\_nodes\_a|int|Yes|number of Worker nodes in the first zone The value is in the range of 1 to 300.|
|num\_of\_nodes\_b|int|Yes|number of Worker nodes in the second zone The value is in the range of 1 to 300.|
|num\_of\_nodes\_c|int|Yes|number of Worker nodes in the third zone The value is in the range of 1 to 300.|
|login\_password|string|Yes|SSH logon password. A password must be a string of 8 to 30 characters and contain uppercase letters, lowercase letters, numbers, and special symbols. You can choose to set either an SSH logon password or a key\_pair.|
|key\_pair|string|Yes|keypair name. Use either this parameter or login\_password.|
|ssh\_flags|bool|No|whether to enable SSH access over the Internet|
|cloud\_monitor\_flags|bool|No|whether to install the cloud monitoring plugin.|
|public\_slb|bool|No|whether to enable the API server over the Internet. The default setting is true. If you set this parameter to false, the API server over the Internet will not be created, and only the API server over your private network will be created.|

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
"name": "mulit-az-cluster",
"timeout_mins": 60,
"cluster_type": "Kubernetes",
"region_id": "cn-shanghai",
"multi_az": true,
"container_cidr": "10.4.0.0/16",
"service_cidr": "10.3.0.0/20",
"vpcid": "vpc-mytestvpc",
"vswitch_id_a": "vsw-a",
"vswitch_id_b": "vsw-b",
"vswitch_id_c": "vsw-c",
"master_instance_type_a": "ecs.c5.large",
"master_instance_type_b": "ecs.d1.2xlarge",
"master_instance_type_c": "ecs.c4.xlarge",
"master_system_disk_category": "cloud_efficiency",
"master_system_disk_size": 40,
"worker_instance_type_a": "ecs.c5.large",
"worker_instance_type_b": "ecs.d1.2xlarge",
"worker_instance_type_c": "ecs.c4.xlarge",
"worker_system_disk_category": "cloud_efficiency",
"worker_system_disk_size": 40,
"num_of_nodes_a": 2,
"num_of_nodes_b": 2,
"num_of_nodes_c": 2,
"ssh_flags": true,
"login_password": "Hello1234",
"cloud_monitor_flags": true

}
```

**Response example**

```
HTTP/1.1 202 Accepted
<Public response header>
{
"cluster_id": "cb95aa626a47740afbf6aa099b650d7ce",
"request_id": "687C5BAA-D103-4993-884B-C35E4314A1E1",
"task_id": "T-5a54309c80282e39ea00002f"
}
```

