# Create a managed Kubernetes cluster {#reference_af2_kc1_dfb .reference}

This topic describes how to create a managed Kubernetes cluster. You must specify the number of Worker nodes for the cluster.

## Request information {#section_dt4_pl2_xdb .section}

**Request line**

``` {#codeblock_yj9_9c7_z0v}
POST /clusters HTTP/1.1 
```

**Special request header**

None. See [Public request headers](reseller.en-US/Developer Guide/Cluster API call method/Common parameters.md#section_mr5_lf1_wdb).

**Request body**

``` {#codeblock_zqz_yl4_xbo}
{
    "disable_rollback": "whether or not to roll back if the cluster fails to be created",
    "name": "cluster name",
    "timeout_mins": "timeout for creating the cluster",
    "cluster_type": "cluster type, Managed Kubernetes",
    "region_id": "region",
    "vpcid": "Virtual Private Cloud (VPC) ID",
    "vswitch_ids": "IDs of VSwitches. You must set one VSwitch at least, and can set five VSwitches at most.", 
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
    "worker_instance_types": "instance types of worker nodes",
    "worker_system_disk_category": "system disk type of worker nodes",
    "worker_system_disk_size": "system disk size of worker nodes",
    "worker_data_disk":"whether or not to mount data disks true|false",
    "data_disk_category": "data disk category",
    "worker_data_disk_size":"data disk size",
    "num_of_nodes": "number of worker nodes",
    "snat_entry": "whether or not to configure the SNATEntry",
    "public_slb":"whether to associate an EIP with the intranet SLB instace",
    "proxy_mode": "the proxy mode. Valid value: iptables|ipvs",
    "addons": "the addon. This is an object in the array format.", 
    "tags": "the tag to be add to the cluster. This is an object in the array format.",
}
```

**Request body explanation**

|Name|Type|Required|Description|
|----|----|--------|-----------|
|cluster\_type|string|Yes|The cluster type.|
|key\_pair|string|Yes|key pair name. Use either this parameter or login\_password.|
|login\_password|string|Yes|SSH logon password. The password must be a string of 8 to 30 characters and contain uppercase letters, lowercase letters, numbers, and symbols. Choose either this parameter or key\_pair.|
|name|string|Yes|The cluster name. A cluster name can contain uppercase letters, lowercase letters, Chinese characters, numbers, and hyphens \(-\).|
|num\_of\_nodes|int|Yes|The number of Worker nodes. The value is in the range of 0 to 300.|
|region\_id|string|Yes|The ID of the region in which the cluster is located.|
|snat\_entry|bool|Yes|Whether or not to configure the SNAT for VPC. This parameter must be set to true if a VPC is created automatically. Configure the parameter value according to the outbound capability if you select to use an existing VPC to create the cluster.|
|vswitch\_ids|list|Yes|The IDs of VSwitches. You must set one VSwitch at least, and can set three VSwitches at most.|
|worker\_system\_disk\_category|string|Yes|The type of system disks attached to Worker nodes.|
|worker\_system\_disk\_size|int|Yes|The size of a system disk attached to Worker nodes, in GiB.|
|addons|list|No|The addons installed on the Kubernetes cluster. -   An addon requires the following parameters:
    -   name: Required.
    -   version: Optional. By default, the latest version is used.
    -   config: Optional.
-   Network plugin: Select Flannel or Terway.
-   Log Service plugin: Optional. If you do not install this plugin, the audit logs of the cluster cannot be collected.

 |
|container\_cidr|string|No|The pod CIDR block, which cannot overlap with the VPC CIDR block If you choose to enable the system to automatically create a VPC, the 172.16.0.0/16 pod CIDR block is used by default.|
|cloud\_monitor\_flags|bool|No|Whether to install the cloud monitoring plugin.|
|disable\_rollback|bool|No|Whether to install the cloud monitoring plugin. -   true: indicates to install the plugin
-   false: indicates not to install the plugin

 |
|proxy\_mode|string|No|The kube-proxy mode. The modes iptables and ipvs are supported. The default is iptables.|
|public\_slb|bool|No|Whether or not to enable the Internet API server: -   true: indicates to enable the Internet API server. This is the default value.
-   false: indicates not to create the Internet API server. If you select this value, only the intranet API server will be created.

 |
|service\_cidr|string|No|The service CIDR block that cannot overlap with the VPC CIDR block or the pod CIDR block If you choose to enable the system to automatically create a VPC, the 172.19.0.0/20 service CIDR block is used by default.|
|tags|list|No|The tag to be added to the cluster. -   key: tag name.
-   value: tag value.

 |
|timeout\_mins|int|No|The timeout \(in minutes\) for creating the cluster resource stack. The default value is 60.|
|vpcid|string|No|VPC ID, which can be empty. If you do not set any VPC, the system automatically creates a VPC that belongs to the CIDR block 192.168.0.0/16. **Note:** The vpcid parameter and the vswitchid parameter must be both set to empty or relevant values.

 |
|worker\_auto\_renew|bool|No|Whether to enable Worker node automatic renew. Available values are: -   true: indicates to enable automatic renew
-   false: indicates not to enable automatic renew

 |
|worker\_auto\_renew\_period|int|No|The automatic renew period. This parameter setting takes effect and becomes required only if you select the PrePaid payment type and enable automatic renew. Available values for different Subscription period units are as follows: -   \{“1”, “2”, “3”\} for `PeriodUnit=Week`
-   \{“1”, “2”, “3”, “6”, “12”\} for `PeriodUnit=Month`

 |
|worker\_data\_disk|string|No|Whether to amount data disks. Available values are: -   true: indicates to mount data disks to Worker nodes
-   false: indicates not to mount data disks to Worker nodes

 |
|worker\_data\_disk\_category|int|No|Data disk type.|
|worker\_data\_disk\_size|string|No|Data disk size|
|worker\_instance\_charge\_type|string|No|Worker node payment type. -   PrePaid: indicates Subscription
-   PostPaid: indicates Pay-As-You-Go

 |
|worker\_period|int|No|Subscription period. This parameter setting takes effect and becomes required only if the value of worker\_instance\_charge\_type is set to PrePaid. The parameter values for different Subscription period units are as follows: -   \{“1”, “2”, “3”, “4”\} for `PeriodUnit=Week`
-   \{ “1”, “2”, “3”, “4”, “5”, “6”, “7”, “8”, “9”, “12”, “24”, “36”,”48”,”60”\} for `PeriodUnit=Month`

 |
|worker\_period\_unit|string|No|Whether to enable Worker node automatic renew. Available values are: -   true: indicates to enable automatic renew
-   false: indicates not to enable automatic renew

 |

## Response information {#section_ljv_tm2_xdb .section}

**Response line**

``` {#codeblock_45j_56l_dft}
HTTP/1.1 202 Accepted
```

**Special response header**

None. See [Public response headers](reseller.en-US/Developer Guide/Cluster API call method/Common parameters.md#section_zr5_lf1_wdb).

**Response body**

``` {#codeblock_obc_h1n_nle}
{
"cluster_id":"string",
"request_id":"string",
"task_id":"string"
}
```

## Examples {#section_hcw_wm2_xdb .section}

**Request example**

``` {#codeblock_2na_9np_15p}
POST /clusters HTTP/1.1
<Public response header>
{
"name":"test",
"cluster_type":"my-test-Kubernetes-cluster",
"disable_rollback":true,
"timeout_mins":60,
"kubernetes_version":"1.12.6-aliyun.1",
"region_id":"cn-beijing",
"snat_entry":true,
"cloud_monitor_flags":false,
"public_slb":false,
"node_cidr_mask":"25",
"proxy_mode":"ipvs",
"tags":[],
"addons":[{"name":"flannel"}, {"name":"nginx-ingress-controller"}],
"worker_instance_types":["ecs.hfc5.xlarge"],
"num_of_nodes":3,
"worker_system_disk_category":"cloud_efficiency",
"worker_system_disk_size":120,
"worker_instance_charge_type":"PostPaid",
"vpcid":"vpc-2zegvl5etah5requ09nec",
"container_cidr":"172.20.0.0/16",
"service_cidr":"172.21.0.0/20",
"vswitch_ids":["vsw-2ze48rkq464rsdts1****"],
"login_password":"test@19****"
}
```

**Response example**

``` {#codeblock_8vc_609_i7l}
HTTP/1.1 202 Accepted
<Public response header>
{
    "cluster_id": "cb95aa626a47740afbf6aa099b65****",
    "request_id": "687C5BAA-D103-4993-884B-C35E4314A1E1",
    "task_id": "T-5a54309c80282e39ea00002f"
}
```

