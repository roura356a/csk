# Create a Kubernetes cluster {#reference_rls_pwb_wdb .reference}

This topic describes how to create a Kubernetes cluster. For more information about the related APIs, see [Create a Kubernetes cluster](reseller.en-US/Developer Guide/Cluster API list/Create a Kubernetes cluster.md#), [Create a multi-zone Kubernetes cluster](reseller.en-US/Developer Guide/Cluster API list/Create a multi-zone Kubernetes cluster.md#), and [Create a multi-zone Kubernetes cluster](reseller.en-US/Developer Guide/Cluster API list/Create a multi-zone Kubernetes cluster.md#).

## API request and response {#section_ctn_swb_wdb .section}

**Request format**

``` {#codeblock_ylm_snm_f5i}
aliyun cs  POST /clusters --header "Content-Type=application/json" --body "$(cat create.json)"
```

Parameter descriptions:

-   `--header`: Specify Content-Type as application/json.
-   `--body`: This is the body content to be sent to the server. The content can be read from a local file and must be in JSON format. The content of `create.json` is as follows:

**Dedicated Kubernetes cluster** 

``` {#codeblock_6aa_is1_98h}
{
    "disable_rollback": "disable_rollback": "indicates whether to roll back the cluster if the cluster creation fails",
    "name": "cluster name",
    "timeout_mins": cluster creation timeout,
    "cluster_type": "Kubernetes",
    "region_id": "region",
    "vpcid": "VPC ID",
    "master_vswitch_ids": "the IDs of VSwitches on Master nodes. You must set three VSwitches that are located in different zones to ensure high availability",
    "master_instance_types": "the types of ECS instances used by Master nodes. You must set three instance types."
    "master_count": "the number of Master nodes. Valid values: 3|5."
    "container_cidr": "pod Classless Inter-Domain Routing (CIDR) block",
    "service_cidr": "service CIDR block",
    "ssh_flags": "enable SSH access over the Internet. Valid values: true | false.",
    "cloud_monitor_flags":"install the CloudMonitor plugin. Valid values: true | false.",
    "login_password": "password used to log on to cluster nodes by using SSH. This parameter and the key_pair parameter are mutually exclusive.",
    "key_pair":"the key pair used to log on to cluster nodes by using SSH. This parameter and the login_password parameter are mutually exclusive.",
    "master_instance_charge_type":"the billing method of Master node instances. Valid values: PostPaid|PrePaid",
    "master_period_unit":"Subscription unit that includes Month and Year. This parameter takes effect only for the PrePaid billing method",
    "master_period":"Subscription period, which takes effect only for the PrePaid billing method",
    "master_auto_renew":"enable Master node automatic renew. Valid values: true | false.",
    "master_auto_renew_period":"Master node renew period",
    "master_system_disk_category": "Master node system disk type",
    "master_system_disk_size":"Master node system disk size",
    "master_data_disk":"mount data disks to the Master node. Valid values: true | false.",
    "master_data_disk_category":"Master node data disk type",
    "master_data_disk_size":"Master node data disk size",
    "worker_instance_charge_type":"the billing method of Worker node instances. Valid values: PostPaid|PrePaid",
    "worker_period_unit":"Subscription unit that includes Month and Year. This parameter takes effect only for the PrePaid billing method",
    "worker_period":"Subscription period, which takes effect only for the PrePaid billing method",
    "worker_auto_renew":"enable Worker node automatic renew. Valid values: true | false.",
    "worker_auto_renew_period":"Worker node renew period",
    "worker_instance_types": "Worker node instance types. ",
    "worker_vswitch_ids": "the IDs of VSwitches. The value range of the number of VSwitches is 1 to 5.",
    "worker_system_disk_category": "Worker node system disk type",
    "worker_system_disk_size": "Worker node system disk size",
    "worker_data_disk":"mount data disks to the Worker node. Valid values: true | false.",
    "worker_data_disk_category":"Worker node data disk type",
    "worker_data_disk_size":"Worker node data disk size",
    "num_of_nodes": "the number of Worker nodes",
    "snat_entry": Configure an SNAT entry. Valid values: true | false.
    "public_slb":"associate an EIP with the intranet SLB instance. Valid values: true | false.",
    "cpu_policy": "static|none",
    "node_port_range": "value range of node ports. The default value range is 30000 to 32767",
    "proxy_mode": "iptables|ipvs",
    "addons": "set addons. This is an array object.", 
    "tags": "set tags for the cluster. This is an array object.",
}
```

**Managed Kubernetes cluster** 

``` {#codeblock_pib_r59_w3p}
{
"disable_rollback": "indicates whether to roll back the cluster if the cluster creation fails"
"name": "cluster name"
"timeout_mins": "cluster creation timeout"
"cluster_type": "Managed Kubernetes"
"region_id": "region."
"vpcid": "VPC ID "    
"vswitch_ids": "the IDs of VSwitches. The value range of the number of VSwitches is 1 to 5.",
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
"worker_instance_types": "Worker instance types"
"worker_system_disk_category": "Worker node system disk type"
"worker_system_disk_size": "Worker node system disk size"
"worker_data_disk":"whether to mount data disks to the worker node. Available values are true and false."
"worker_data_disk_category":"data disk type"
"worker_data_disk_size":"data disk size"
"num_of_nodes": "number of worker nodes"
"snat_entry": "whether to set an SNAT entry"
"public_slb":"associate an EIP with the intranet SLB instance. Valid values: true | false.",
"proxy_mode": "iptables|ipvs",
"addons": "set addons. This is an array object.", 
"tags": "set tags for the cluster. This is an array object.",
}
            }
```

**Serverless Kubernetes cluster**

``` {#codeblock_u5p_7ez_1h8}



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

``` {#codeblock_zk6_vs2_k5n}
{
    "cluster_id": "c61cf530524474386a7ab5a1c192****"
    "request_id": "348D4C9C-9105-4A1B-A86E-B58F0F875575"
    "task_id": "T-5ad724ab94a2b109e8000004"
}
```

