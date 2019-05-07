# Create a cluster {#reference_rls_pwb_wdb .reference}

This topic describes how to create a cluster and the specified number of nodes. For API descriptions, see [Create a Kubernetes cluster](reseller.en-US/Developer Guide/Cluster API list/Create a Kubernetes cluster.md#), [Create a multi-zone Kubernetes cluster](reseller.en-US/Developer Guide/Cluster API list/Create a multi-zone Kubernetes cluster.md#), and [Create a multi-zone Kubernetes cluster](reseller.en-US/Developer Guide/Cluster API list/Create a multi-zone Kubernetes cluster.md#).

## API request and response {#section_ctn_swb_wdb .section}

**Request format**

```
aliyun cs  POST /clusters --header "Content-Type=application/json" --body "$(cat create.json)"
```

Parameter descriptions:

-   `--header`: Specify Content-Type as application/json.
-   `--body`: This is the body content to be sent to the server. The content can be read from a local file and must be in JSON format. The content of `create.json` is as follows:

**Kubernetes cluster with a single zone** 

```
{
"disable_rollback": "indicates whether to roll back the cluster if the cluster creation fails"
"name": "cluster name"
"timeout_mins": "cluster creation timeout"
"cluster_type": "Kubernetes"
"region_id": "region"
"vpcid": "Virtual Private Cloud (VPC) ID"
"zoneid": "zone"
"vswitchid": "VSwitch ID" 
"container_cidr": "pod Classless Inter-Domain Routing (CIDR)"
"service_cidr": "service CIDR"
"ssh_flags": "whether to enable SSH access over the Internet" 
"cloud_monitor_flags":"whether to install the cloud monitoring plugin"
"login_password": "password used to log on to the node by using SSH. Use either this parameter or the key_pair"
"key_pair":"key pair name. Use either this parameter or login_password"
"master_instance_charge_type":"Master instance payment type that includes PostPaid and PrePaid"
"master_period_unit":"Subscription unit that includes Month and Year. This parameter takes effect only for the PrePaid payment type"
"master_period":"Subscription period, which takes effect only for the PrePaid payment type"
"master_auto_renew":"whether to enable Master node automatic renew"
"master_auto_renew_period":"Master node renew period"
"master_instance_type": "Master instance type"
"master_system_disk_category": "Master node system disk type"
"master_system_disk_size":"Master node system disk size"
"master_data_disk":"whether to mount data disks to the Master node"
"master_system_disk_category": "Master node data disk type"
"master_system_disk_size":"Master node data disk size"
"worker_instance_charge_type":"Worker node payment type that includes PrePaid and PostPaid"
"worker_period_unit":"Subscription unit that includes Month and Year. This parameter takes effect only for the PrePaid payment type"
"worker_period":"Subscription period that takes effect only for the PrePaid payment type"
"worker_auto_renew":"whether to enable Worker node automatic renew. Available values are true and false."
"worker_auto_renew_period":"Worker node renew period"
"worker_instance_type": "Worker instance type"
"worker_system_disk_category": "Worker node system disk type"
"worker_system_disk_size": "Worker node system disk size"
"worker_data_disk":"whether to mount data disks to the Worker node"
"worker_data_disk_category":"Worker node data disk type"
"worker_data_disk_size":"Worker node data disk size"
"num_of_nodes": "number of Worker nodes"
"snat_entry": "whether to set an SNAT entry"
"public_slb":"whether to create the SLB corresponding to the Internet API server"
} 
```

**Kubernetes cluster with multiple zones** 

```
{
"disable_rollback": "indicates whether to roll back the cluster if the cluster creation fails"
"name": "cluster name"
"timeout_mins": "cluster creation timeout"
"cluster_type": "Kubernetes"
"region_id": "region"
"multi_az": true
"vpcid": "VPC ID "
"container_cidr": "pod CIDR block" 
"service_cidr": "service CIDR block"
"vswitch_id_a": "ID of the VSwitch in the first zone"
"vswitch_id_b": "ID of the VSwitch in the second zone"
"vswitch_id_c": "ID of the VSwitch in the third zone" 
"master_instance_type_a": "instance type of the Master node in the first zone"
"master_instance_type_b": "instance type of the Master node in the second zone"
"master_instance_type_c": "instance type of the Master node in the third zone" 
"master_instance_charge_type":"Master instance payment type that includes PostPaid and PrePaid"
"master_period_unit":"Subscription unit that includes Month and Year. This parameter takes effect only for the PrePaid payment type"
"master_period":"Subscription period that takes effect only for the PrePaid payment type"
"master_auto_renew":"whether to enable Master node automatic renew"
"master_auto_renew_period":"Master node renew period" 
"master_system_disk_category": "Master node system disk type"
"master_system_disk_size": "Master node system disk size" 
"master_data_disk":"whether to mount data disks to the Master node"
"master_data_disk_category":"Master node data disk type"
"master_data_disk_size":"Master node data disk size" 
"worker_instance_type_a": "instance type of the Worker node in the first zone"
"worker_instance_type_b": "instance type of the Worker node in the second zone"
"worker_instance_type_c": "instance type of the Worker node in the third zone"
"worker_instance_charge_type":"Worker node payment type that includes PrePaid and PostPaid"
"worker_period_unit":"Subscription unit that includes Month and Year. This parameter takes effect only for the PrePaid payment type"
"worker_period":"Subscription period that takes effect only for the PrePaid payment type."
"worker_auto_renew":"whether to enable Worker node automatic renew. Available values are true and false."
"worker_auto_renew_period":"Worker node renew period" 
"worker_system_disk_category": "Worker node system disk type"
"worker_system_disk_size": "Worker node system disk size"
"worker_data_disk":"whether to mount data disks to the Worker node"
"worker_data_disk_category":"Worker node data disk type"
"worker_data_disk_size":"Worker node data disk size" 
"num_of_nodes_a": "number of Worker nodes in the first zone"
"num_of_nodes_b": "number of Worker nodes in the second zone"
"num_of_nodes_c": "number of Worker nodes in the third zone"
"ssh_flags": "whether to enable SSH access over the Internet"
"login_password": "SSH logon password" 
"cloud_monitor_flags":"whether to install the cloud monitoring plugin"
"public_slb":"whether to create the SLB corresponding to the Internet API server"
}
```

**Managed Kubernetes cluster** 

```
{
"disable_rollback": "indicates whether to roll back the cluster if the cluster creation fails"
"name": "cluster name"
"timeout_mins": "cluster creation timeout"
"cluster_type": "ManagedKubernetes"
"region_id": "region. Available values are cn-beijing and cn-hangzhou"
"vpcid": "VPC ID "
"zoneid": "zone"
"vswitchid": "VSwitch ID" 
"container_cidr": "pod CIDR block"
"service_cidr": "service CIDR block"
"cloud_monitor_flags":"whether to install the cloud monitoring plugin"
"login_password": "password used to log on to the node by using SSH. Use either this parameter or the key_pair."
"key_pair":"key pair name. use either this parameter or login_password."
"worker_instance_charge_type":"Worker node payment type that includes PrePaid and PostPaid"
"worker_period_unit":"Subscription unit that includes Month and Year. This parameter takes effect only for the PrePaid payment type"
"worker_period":"Subscription period, which takes effect only for the PrePaid payment type."
"worker_auto_renew":"whether to enable Worker node automatic renew. Available values are true and false."
"worker_auto_renew_period":"Worker node renew period"
"worker_instance_type": "Worker instance type"
"worker_system_disk_category": "Worker node system disk type"
"worker_system_disk_size": "Worker node system disk size"
"worker_data_disk":"whether to mount data disks to the worker node. Available values are true and false."
"worker_data_disk_category":"data disk type"
"worker_data_disk_size":"data disk size"
"num_of_nodes": "number of worker nodes"
"snat_entry": "whether to set an SNAT entry"
}ntry": whether to set an SNAT entry
			}
```

**Serverless Kubernetes cluster**

```

{
"cluster_type": "Ask", 
"name": "indicates the name of a serverless Kubernetes cluster",
"region_id": "indicates a region",
"nat_gateway": indicates whether to create a NAT gateway. Valid value: true | false.
"private_zone": indicates whether to enable PrivatreZone for service discovery in a VPC. Valid value: true | false.
"vpc_id": "indicates the ID of a VPC". If you do not set this parameter, then the system automatically creates a VPC.
"vswitch_id": "indicates a Vswitch ID"
}
```

**Response result**

```
{
    "cluster_id": "c61cf530524474386a7ab5a1c192a0d57"
    "request_id": "348D4C9C-9105-4A1B-A86E-B58F0F875575"
    "task_id": "T-5ad724ab94a2b109e8000004"
}
```

