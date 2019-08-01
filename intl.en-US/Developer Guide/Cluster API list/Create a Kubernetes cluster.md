# Create a Kubernetes cluster {#reference_cpd_gl2_xdb .reference}

This topic describes how to create a Kubernetes cluster and a specified number of nodes.

## Request information {#section_dt4_pl2_xdb .section}

**Request line**

``` {#codeblock_sg4_j9h_w4s}
POST /clusters HTTP/1.1
```

**Special request header**

None. See [Public request headers](reseller.en-US/Developer Guide/Cluster API call method/Common parameters.md#section_mr5_lf1_wdb).

**Request body**

``` {#codeblock_ecs_eyg_hqy}
{
    "disable_rollback": "whether to roll back the cluster if the cluster creation fails"
    "name": "cluster name"
    "timeout_mins": cluster creation timeout
    "cluster_type": "cluster type, namely, Kubernetes"
    "region_id": "region"
    "vpcid": "Virtual Private Cloud (VPC) ID"
    "master_vswitch_ids": "the IDs of VSwitches on Master nodes. Set three VSwitches that are located in different regions to ensure the cluster is highly available",
    "master_instance_types": "the type of ECS instances that used by the Master nodes. Set three types of ECS instances"
    "master_count": "the number of Master nodes. Valid value: 3|5."
    "container_cidr": "pod Classless Inter-Domain Routing (CIDR) block"
    "service_cidr": "service CIDR block"
    "ssh_flags": "whether to enable SSH access over the Internet"    
    "cloud_monitor_flags":"whether to install the cloud monitoring plugin"
    "login_password": "password used to log on to the node by using SSH. Use either this parameter or the key_pair."
    "key_pair":"key pair name. Use either this parameter or login_password."
    "master_instance_charge_type":"Master instance payment type that includes PostPaid and PrePaid"
    "master_period_unit":"Subscription unit that which includes month and year, and takes effect only for the PrePaid payment type"
    "master_period":"Subscription period that takes effect only for the PrePaid payment type"
    "master_auto_renew":"whether to enable master node automatic renew"
    "master_auto_renew_period":"Master node renew period"
    "master_system_disk_category": "Master node system disk type"
    "master_system_disk_size":"Master system disk size"
    "master_data_disk":"whether to mount data disk to the Master node"
    "master_system_disk_category": "Master node data disk type"
    "master_system_disk_size":"Master node data disk size"
    "worker_instance_charge_type":"Worker node payment type that includes PrePaid and PostPaid"
    "worker_period_unit":"Subscription unit that includes Month and Year, and takes effect only for the PrePaid payment type"
    "worker_period":"Subscription period that takes effect only for the PrePaid payment type"
    "worker_auto_renew":"whether to enable worker node automatic renew. Available values are true and false."
    "worker_auto_renew_period":"Worker node renew period"
    "worker_instance_types": "Worker instance types"
    "worker_vswitch_ids": "IDs of VSwitches. You must set one VSwitch at least, and can set five VSwitches at most",
    "worker_system_disk_category": "Worker node system disk type"
    "worker_system_disk_size": "Worker node system disk size"
    "worker_data_disk":"whether to mount data disks to the Worker node"
    "worker_data_disk_category":"Worker node data disk type"
    "worker_data_disk_size":"Worker node data disk size"
    "num_of_nodes": "number of Worker nodes"
    "snat_entry": "whether to set an SNAT entry"
    "public_slb":"whether to associate an EIP with the intranet SLB instace",
    "cpu_policy": "static|none",
    "node_port_range": "the range of the node port. The default is 30,000 to 65,535",
    "proxy_mode": "the proxy mode. Valid value: iptables|ipvs",
    "addons": "the addon. This is an object in the array format.", 
    "tags": "the tag to be add to the cluster. This is an object in the array format.",
}
```

**Request body description**

|Name|Type|Required|Description|
|----|----|--------|-----------|
|cluster\_type|String|Yes|The cluster type.|
|key\_pair|String|Yes|key pair name. Use either this parameter or login\_password.|
|login\_password|String|Yes|SSH logon password. The password must be a string of 8 to 30 characters and contain uppercase letters, lowercase letters, numbers, and symbols. Choose either this parameter or key\_pair.|
|master\_instance\_charge\_type|String|Yes|Master node payment type -   PrePaid: Subscription.
-   PostPaid: Pay-As-You-Go. This is the default payment type.

 |
|master\_instance\_types|list|Yes|The types of ECS instances that used by the Master nodes. For more information, see [Instance type families](../../../../reseller.en-US/Instances/Instance type families.md#).|
|master\_system\_disk\_category|String|Yes|The type of system disks attached to Master nodes. Available values are: -   cloud\_efficiency: indicates an Ultra cloud disk.
-   cloud\_ssd: indicates an SSD cloud disk.

 |
|master\_system\_disk\_size|Int|Yes|The size of a system disk attached to Master nodes, in GiB.|
|master\_vswitch\_ids|list|Yes|The IDs of VSwitches on Master nodes. You must set one VSwitch at least, and can set three VSwitches at most. Set three VSwitches that are located in different regions to ensure the cluster is highly available.|
|num\_of\_nodes|Int|Yes|The number of Worker nodes. The value is in the range of 0 to 300.|
|name|String|Yes|The cluster name. A cluster name can contain uppercase letters, lowercase letters, Chinese characters, numbers, and hyphens \(-\).|
|region\_id|String|Yes|The ID of the region in which the cluster is located.|
|snat\_entry|bool|Yes|Whether to set an SNAT entry for the container network. -   If the existing VPC has the capability to access the Internet, you need to set this parameter to false.
-   If the existing VPC does not have the capability to access the Internet, setting this parameter to true indicates to set an SNAT entry; setting this parameter to false indicates not to set any SNAT entry and the Internet cannot be accessed.

 |
|worker\_instance\_types|list|Yes|The types of ECS instances used by Worker nodes. For more information, see [Instance type families](../../../../reseller.en-US/Instances/Instance type families.md#).|
|worker\_system\_disk\_category|String|Yes|The type of system disks attached to Worker nodes.|
|worker\_system\_disk\_size|Int|Yes|The size of a system disk attached to Worker nodes, in GiB.|
|worker\_vswitch\_ids|list|Yes|The IDs of VSwitches on Worker nodes.|
|addons|list|No|The addons installed on the Kubernetes cluster. -   An addon requires the following parameters:
    -   name: Required.
    -   version: Optional. By default, the latest version is used.
    -   config: Optional.
-   Network plugin: Select Flannel or Terway.
-   Log Service plugin: Optional. If you do not install this plugin, the audit logs of the cluster cannot be collected.

 |
|container\_cidr|String|No|The pod CIDR block, which cannot overlap with the VPC CIDR block If you choose to enable the system to automatically create a VPC, the 172.16.0.0/16 pod CIDR block is used by default.|
|cloud\_monitor\_flags|bool|No|Whether to install the cloud monitoring plugin. -   true: indicates to install the plugin
-   false: indicates not to install the plugin

 |
|cpu\_policy|String|No|The CPU policy. If the Kubernetes cluster is V1.12.6 or later, you can set static or none. The default is none.|
|disable\_rollback|bool|No|Whether to roll back the cluster if the cluster creation fails. -   true: indicates not to roll back if the cluster creation fails.
-   false: indicates to roll back if the cluster creation fails.

 The default is true. If you select to roll back, resources generated in the creation process will be released. We recommend that you do not select false.|
|master\_auto\_renew|bool|No|Whether to enable Master node automatic renew. This parameter setting takes effect only if the value ofmaster\_instance\_charge\_type is set to PrePaid. Available values are: -   true: indicates to enable automatic renew
-   false: indicates not to enable automatic renew

 |
|master\_auto\_renew\_period|Int|No|The automatic renew period. This parameter setting takes effect and becomes required only if you select the PrePaid payment type and enable automatic renew. Available values for different Subscription period units are as follows: -   \{“1”, “2”, “3”\} for `PeriodUnit=Week`
-   \{“1”, “2”, “3”, “6”, “12”\} for `PeriodUnit=Month`

 |
|master\_count|Int|No|The number of Master nodes. Valid value: 3|5. The default is 3.|
|master\_data\_disk|bool|No|Whether to mount data disks to the Master node. Available values are: -   true: indicates to mount data disks
-   false: indicates not to mount data disks. This is the default .

 |
|master\_data\_disk\_category|String|No|Master node data disk type. This parameter setting takes effect only if data disks are mounted to the Master node. Available values are: -   cloud: indicates a basic cloud disk
-   cloud\_efficiency: indicates an Ultra cloud disk
-   cloud\_ssd: indicates an SSD cloud disk

 |
|master\_data\_disk\_size|Int|No|Master node data disk size in GiB. This parameter setting takes effect only if data disks are mounted to the Master node.|
|master\_period|Int|No|Subscription period. This parameter setting takes effect and becomes required only if the value of morker\_instance\_charge\_type is set to PrePaid. The parameter values for different Subscription period units are as follows: -   \{“1”, “2”, “3”, “4”\} for `PeriodUnit=Week`
-   \{ “1”, “2”, “3”, “4”, “5”, “6”, “7”, “8”, “9”, “12”, “24”, “36”,”48”,”60”\} for `PeriodUnit=Month`

 |
|master\_period\_unit|String|No|If you select the PrePaid payment type, you need to specify a Subscription period that uses either of the following units: -   Week: one week is used as the timing unit.
-   Month: one month is used as the timing unit.

 |
|node\_port\_range|String|No|The range of the node port. The default is 30,000 to 65,535|
|proxy\_mode|String|No|The kube-proxy mode. The modes iptables and ipvs are supported. The default is iptables.|
|public\_slb|bool|No|Whether or not to enable the Internet API server: -   true: indicates to enable the Internet API server. This is the default value.
-   false: indicates not to create the Internet API server. If you select this value, only the intranet API server will be created.

 |
|service\_cidr|String|No|The service CIDR block that cannot overlap with the VPC CIDR block or the pod CIDR block If you choose to enable the system to automatically create a VPC, the 172.19.0.0/20 service CIDR block is used by default.|
|ssh\_flags|bool|No|Whether to enable SSH access over the Internet. -   true: yes
-   false: no

 |
|tags|list|No|The tag to be added to the cluster. -   key: tag name.
-   value: tag value.

 |
|timeout\_mins|Int|No|The timeout \(in minutes\) for creating the cluster resource stack. The default value is 60.|
|vpcid|String|No|VPC ID, which can be empty. If you do not set any VPC, the system automatically creates a VPC that belongs to the CIDR block 192.168.0.0/16. **Note:** The vpcid parameter and the vswitchid parameter must be both set to empty or relevant values.

 |
|worker\_auto\_renew|bool|No|Whether to enable Worker node automatic renew. Available values are: -   true: indicates to enable automatic renew
-   false: indicates not to enable automatic renew

 |
|worker\_auto\_renew\_period|Int|No|The automatic renew period. This parameter setting takes effect and becomes required only if you select the PrePaid payment type and enable automatic renew. Available values for different Subscription period units are as follows: -   \{“1”, “2”, “3”\} for `PeriodUnit=Week`
-   \{“1”, “2”, “3”, “6”, “12”\} for `PeriodUnit=Month`

 |
|worker\_data\_disk|String|No|Whether to amount data disks. Available values are: -   true: indicates to mount data disks to Worker nodes
-   false: indicates not to mount data disks to Worker nodes

 |
|worker\_data\_disk\_category|Int|No|Data disk type. This parameter setting takes effect only if you mount data disks to the Worker nodes. Available values are: -   cloud: indicate a basic cloud disk
-   cloud\_efficiency: indicates an Ultra cloud disk
-   cloud\_ssd: indicates an SSD cloud disk

 |
|worker\_data\_disk\_size|String|No|Data disk size in GiB. This parameter setting takes effect only if data disks are mounted to the Worker node.|
|worker\_instance\_charge\_type|String|No|Worker node payment type. The default value is PostPaid. Available values are: -   PrePaid: indicates Subscription
-   PostPaid: indicates Pay-As-You-Go

 |
|worker\_period|Int|No|Subscription period. This parameter setting takes effect and becomes required only if the value of worker\_instance\_charge\_type is set to PrePaid. The parameter values for different Subscription period units are as follows: -   \{“1”, “2”, “3”, “4”\} for `PeriodUnit=Week`
-   \{ “1”, “2”, “3”, “4”, “5”, “6”, “7”, “8”, “9”, “12”, “24”, “36”,”48”,”60”\} for `PeriodUnit=Month`

 |
|worker\_period\_unit|String|No|Whether to enable Worker node automatic renew. Available values are: -   true: indicates to enable automatic renew
-   false: indicates not to enable automatic renew

 |

## Response information {#section_ljv_tm2_xdb .section}

**Response line**

``` {#codeblock_yn7_5ou_ut6}
HTTP/1.1 202 Accepted
```

**Special response header**

None. See [Public response headers](reseller.en-US/Developer Guide/Cluster API call method/Common parameters.md#section_zr5_lf1_wdb).

**Response body**

|Name|Type|Description|
|----|----|-----------|
|cluster\_id|String|The cluster ID.|
|request\_id|String|The request ID.|
|task\_id|String|The task ID. It is automatically assigned by the system. You can use it to check the status of a task.|

## Examples {#section_hcw_wm2_xdb .section}

**Request example**

``` {#codeblock_bv1_ifo_7pr}
POST /clusters HTTP/1.1
<Public response header>
{
"cluster_type":"Kubernetes",
"name":"my-test-Kubernetes-cluster",
"region_id":"cn-beijing",
"disable_rollback":true,
"timeout_mins":60,
"kubernetes_version":"1.12.6-aliyun.1",
"snat_entry":true,
"public_slb":false,
"cloud_monitor_flags":false,
"node_cidr_mask":"25",
"proxy_mode":"iptables",
"tags":[],
"addons":[{"name":"flannel"}, {"name":"nginx-ingress-controller"}],
"node_port_range":"30000-32767",
"login_password":"test****",
"cpu_policy":"none",
"master_count":3,
"master_vswitch_ids":["vsw-2ze48rkq464rsdts****","vsw-2ze48rkq464rsdts1****","vsw-2ze48rkq464rsdts1****"],
"master_instance_types":["ecs.sn1.medium","ecs.sn1.medium","ecs.sn1.medium"],
"master_system_disk_category":"cloud_efficiency",
"master_system_disk_size":40,
"worker_instance_types":["ecs.sn2.3xlarge"],
"num_of_nodes":3,
"worker_system_disk_category":"cloud_efficiency",
"worker_system_disk_size":120,
"vpcid":"vpc-2zegvl5etah5requ0****",
"worker_vswitch_ids":["vsw-2ze48rkq464rsdts****"],
"container_cidr":"172.20.XX.XX/16",
"service_cidr":"172.21.XX.XX/20"
}
```

**Response example**

``` {#codeblock_3d5_8w3_0f6}
HTTP/1.1 202 Accepted
<Public response header>
{
    "cluster_id": "cb95aa626a47740afbf6aa099b650****",
    "request_id": "687C5BAA-D103-4993-884B-C35E4314A1E1",
    "task_id": "T-5a54309c80282e39ea00002f"
}
```

