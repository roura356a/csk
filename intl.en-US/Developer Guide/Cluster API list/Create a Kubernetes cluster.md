# Create a Kubernetes cluster {#reference_cpd_gl2_xdb .reference}

Create a Kubernetes cluster with a specified number of new nodes.

## Request information {#section_dt4_pl2_xdb .section}

**Request Line requestline**

```
POST /clusters HTTP/1.1
```

**Special request header requesthead**

None. See [Common parameters](reseller.en-US/Developer Guide/Cluster API call method/Common parameters.md#).

**Request body \(RequestBody\)**

```
{
    "disable_rollback": "whether or not to roll back if the cluster fails to be created",
    "Name": "cluster name ",
    "Maid": cluster creation timeout,
    "cluster_type": "cluster type, Kubernetes",
    "region_id": "region",
    "vpcid": "Virtual Private Cloud (VPC) ID",
    "zoneid": "zone",
    "vswitchid": "VSwitch ID",    
    "container_cidr": "pod Classless Inter-Domain Routing (CIDR)",
    "service_cidr": "service CIDR",
    "ssh_flags": "whether or not to enable SSH access for Internet",    
    "cloud_monitor_flags":"whether or not to install the cloud monitoring plug-in",
    "login_password": "password used to log on to the node by using SSH. Use either this parameter or the key_pair.",
    "key_pair":"key pair name. Use either this parameter or login_password.",
    "master_instance_type": "instance type of master nodes",
    "master_system_disk_category": "system disk type of master nodes",
    "master_system_disk_size":"system disk size of master nodes",
    "worker_instance_type": "instance type of worker nodes",
    "worker_system_disk_category": "system disk type of worker nodes",
    "worker_system_disk_size": "system disk size of worker nodes",
    "num_of_nodes": "number of worker nodes",
    "snat_entry": "whether or not to configure the SNATEntry",
}
```

**Request body explanation**

|Name |Parameters|Required|Description|
|:----|:---------|:-------|:----------|
|disable\_rollback|bool|No |Whether or not to roll back if the cluster fails to be created. true indicates to not roll back and false indicates to roll back. Resources generated in the creation process are released if you select to roll back. We do not recommend that you use false.|
|name |string|Yes|The cluster name, which can contain uppercase English letters, lowercase English letters, Chinese characters, numbers, and hyphens \(-\).|
|timeout\_mins|integer|No |The timeout \(in minutes\) for creating the cluster resource stack. The default value is 60.|
|region\_id|string|Yes|The ID of the region in which the cluster resides.|
|zoneid|string|Yes|The zone of the region in which the cluster resides.|
|vpcid|string|No |The VPC ID, which can be empty. If left empty, the system automatically creates a VPC. The CIDR block of the automatically created VPC is 192.168.0.0/16. The vpcid and vswitchid must be empty or have a certain value at the same time.|
|vswitchid|string|No |The VSwitch ID, which can be empty. If left empty, the system automatically creates a VSwitch. The CIDR block of the automatically created VSwitch is 192.168.0.0/16.|
|container\_cidr|string|No |The pod CIDR block, which cannot conflict with the VPC CIDR block. If you select to allow the system to automatically create a VPC, use the CIDR block 172.16.0.0/16 by default.|
|service\_cidr|string|No |A service segment cannot conflict with a VPC segment and a container segment. If you select to allow the system to automatically create a VPC, use the 172.19.0.0/20 CIDR block by default.|
|master\_instance\_type|string|Yes|The Elastic Compute Service \(ECS\) instance type code of the master nodes. For more information, see [Instance type families](../../../../reseller.en-US/Product Introduction/Instance type families.md#).|
|master\_system\_disk\_category|string|Yes|The system disk type of the master nodes.|
|master\_system\_disk\_size|integer|No| The system disk size of the master nodes.|
|worker\_instance\_type|string|Yes|The Elastic Compute Service \(ECS\) instance type code of the worker nodes. For more details, see.|
|worker\_system\_disk\_category|string|Yes|The system disk type of the worker nodes.|
|worker\_system\_disk\_size|integer|No|The system disk size of the worker nodes.|
|login\_password|string|Yes|The SSH logon password, which is 8–30 characters long and must contain uppercase letters/lowercase letters, numbers, and special characters at the same time. Use either this parameter or the key\_pair.|
|key\_pair|string|Yes|The key pair name. Use either this parameter or login\_password.|
|num\_of\_nodes|integer|No|The number of worker nodes. The value range is \[0,300\].|
|snat\_entry|bool|Yes|Whether or not to configure the SNAT for VPC. This parameter must be set to  true if you select to allow the system to automatically create a VPC. Configure the parameter value according to the outbound capability if you select to use an existing VPC to create the cluster.|
|ssh\_flags|bool|No |Whether or not to enable SSH access for Internet.|
|cloud\_monitor\_flags|bool|No |Whether or not to install the cloud monitoring plug-in.|

## Response information {#section_ljv_tm2_xdb .section}

**Returns the row responseline**

```
HTTP/1.1 202 accepted
```

**Special return head responsehead**

None. See [EN-US\_TP\_7274.md\#section\_zr5\_lf1\_wdb](reseller.en-US/Developer Guide/Kubernetes API reference/Cluster API call method/Common parameters.md#section_zr5_lf1_wdb).

**Response body \(ResponseBody\)**

```
{
    "cluster_id":"string",
    "request_id":"string",
    "task_id":"string"
}
```

## Example {#section_hcw_wm2_xdb .section}

**Request example**

```
POST /clusters HTTP/1.1
<Public request header>
{
    "Maid": True,
    "name": "my-test-Kubernetes-cluster",
    "Maid": 60,
    "cluster_type": "Kubernetes",
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
    "Maid": 40,
    "snat_entry": true,
    "ssh_flags": true,
    "login_password": "Hello1234"
}
```

**Returns an example**

```
HTTP/1.1 202 Accepted
<Public response headers>
{
    "cluster_id": "cb95aa626a47740afbf6aa099b650d7ce",
    "request_id": "687C5BAA-D103-4993-884B-C35E4314A1E1",
    "task_id": "T-5a54309c80282e39ea00002f"
}
```

