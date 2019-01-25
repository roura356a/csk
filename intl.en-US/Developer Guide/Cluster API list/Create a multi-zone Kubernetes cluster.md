# Create a multi-zone Kubernetes cluster {#reference_yz2_qnk_xdb .reference}

This topic describes how to create a highly available Kubernetes cluster across zones with a specified number of nodes.

## Request information {#section_gm1_wnk_xdb .section}

**Request line**

```
POST /clusters HTTP/1.1
```

**Special request header**

None. See [Public request headers](reseller.en-US/Developer Guide/Cluster API call method/Common parameters.md#section_mr5_lf1_wdb).

**Request body**

```
{
    "disable_rollback": "whether or not to roll back if the cluster fails to be scaled out or in",
    "name": "cluster name",
    "timeout_mins": "timeout for creating the cluster",
    "cluster_type": "Kubernetes",
    "region_id": "region",
    "multi_az": true,
    "vpcid": "VPC ID ",
    "container_cidr": "pod Classless Inter-Domain Routing (CIDR)",         
    "service_cidr": "service CIDR",
    "vswitch_id_a": "switch ID of the first available zone",
    "vswitch_id_b": "switch ID of the second available zone",
    "vswitch_id_c": "switch ID of the third available zone",        
    "master_instance_type_a": "specification of instance on the master node in the first available zone",
    "master_instance_type_b": "specification of instance on the master node in the second available zone",
    "master_instance_type_c": "specification of instance on the master node in the third available zone", 
    "master_system_disk_category": "master node system disk type",
    "master_system_disk_size": "master node system disk size",               
    "worker_instance_type_a": "specification of instance on the worker node in the first available zone",
    "worker_instance_type_b": "specification of instance on the worker node in the second available zone",
    "worker_instance_type_c": "specification of instance on the worker node in the third available zone",
    "worker_system_disk_category": "system disk type of worker nodes",
    "worker_system_disk_size": "system disk size of worker nodes",
    "num_of_nodes_a": "number of worker nodes in the first available zone",
    "num_of_nodes_b": "number of worker nodes in the second available zone",
    "num_of_nodes_c": "number of worker nodes in the third available zone",
    "ssh_flags": "whether or not to enable SSH access for Internet",
    "login_password": "SSH login password",             "cloud_monitor_flags":"whether or not to install a cloud monitor plug-in"
}
```

**Request body explanation**

|Name|Type|Required|Description|
|:---|:---|:-------|:----------|
|disable\_rollback|bool|No|Whether or not to roll back if the cluster fails to be scaled out or in. true indicates to not roll back and false indicates to roll back. Resources generated in the creation process are released if you select to roll back. We do not recommend that you use false.|
|name|string|Yes|The cluster name, which can contain uppercase English letters, lowercase English letters, Chinese characters, numbers, and hyphens \(-\).|
|timeout\_mins|int|Yes|The timeout \(in minutes\) for creating the cluster resource stack. The default value is 60.|
|region\_id|string|Yes|The ID of the region in which the cluster resides.|
|multi\_az|bool|Yes|Highly available cluster type, which is fixed at true.|
|vpcid|string|Yes|VPCID|
|container\_cidr|string|No|The pod CIDR block, which cannot conflict with the VPC CIDR block. If you choose to allow the system to automatically create a VPC, the CIDR block 172.16.0.0/16 is used by default.|
|service\_cidr|string|No|The service CIDR block, which cannot conflict with the VPC CIDR block or the pod CIDR block. If you choose to allow the system to automatically create a VPC, the 172.19.0.0/20 CIDR block is used by default.|
|vswitch\_id\_a|string|Yes|Switch ID of the first available zone|
|vswitch\_id\_b|string|Yes|Switch ID of the second available zone|
|vswitch\_id\_c|string|Yes|Switch ID of the third available zone|
|master\_instance\_type\_a|string|Yes|The specification type code of ECS on the master node in the first available zone. For more information, see [Instance type families](../../../../../reseller.en-US/Product Introduction/Instance type families.md#).|
|master\_instance\_type\_b|string|Yes|The specification type code of ECS on the master node in the second available zone. For more information, see [Instance type families](../../../../../reseller.en-US/Product Introduction/Instance type families.md#).|
|master\_instance\_type\_c|string|Yes|The specification type code of ECS on the master node in the third available zone. For more information, see [Instance type families](../../../../../reseller.en-US/Product Introduction/Instance type families.md#).|
|master\_system\_disk\_category|string|Yes|Master node system disk type.|
|master\_system\_disk\_size|int|Yes|Master node system disk size|
|worker\_instance\_type\_a|string|Yes|The specification type code of ECS on the worker node in the first available zone. For more information, see [Instance type families](../../../../../reseller.en-US/Product Introduction/Instance type families.md#).|
|worker\_instance\_type\_b|string|Yes|The specification type code of ECS on the worker node in the second available zone. For more information, see [Instance type families](../../../../../reseller.en-US/Product Introduction/Instance type families.md#).|
|worker\_instance\_type\_c|string|Yes|The specification type code of ECS on the worker node in the third available zone. For more information, see [Instance type families](../../../../../reseller.en-US/Product Introduction/Instance type families.md#).|
|worker\_system\_disk\_category|string|Yes|The system disk type of the worker nodes.|
|worker\_system\_disk\_size|int|Yes|The system disk size of the worker nodes.|
|num\_of\_nodes\_a|int|Yes|The first number of free zone worker nodes. The range is \[1,300\].|
|num\_of\_nodes\_b|int|Yes|The number of worker nodes in the second available zone. The range is \[1,300\].|
|num\_of\_nodes\_c|int|Yes|The number of worker nodes in the third available zone. The range is \[1,300\].|
|login\_password|string|Yes|SSH login password. The password must be 8-30 characters long and contain three types of characters \(uppercase/lowercase letters, numbers, and special characters\) This parameter is exclusive with key\_pair .|
|key\_pair|string|Yes|The keypair name. This parameter is exclusive with login\_password.|
|ssh\_flags|bool|No|Whether or not to enable SSH access for Internet|
|cloud\_monitor\_flags|bool|No|Whether or not to install a cloud monitoring plug-in|

**API note**

-   You first need to include at least three switches under a single VPC. To guarantee high availability, we recommend that you distribute the three switches in different available zones.
-   The switches to be used by the cluster must have access capability \(you can use the nat gateway to configure SNAT rules or use the ECS as a network proxy. We recommend that you use the nat gateway to configure SNAT rules\)

## Return information {#section_zbx_3pk_xdb .section}

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

## Examples {#section_gdx_lpk_xdb .section}

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

