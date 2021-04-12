# Create a managed edge cluster

You can call CreateCluster to create a managed edge cluster.

## Request information

Request line

```
POST /clusters HTTP/1.1 
```

Operation-specific request parameters

No operation-specific request parameters are used. For more information, see [Common parameters](/intl.en-US/API Reference/Common parameters.md).

Request body

```
{
"disable_rollback":"Specifies whether to retain all resources if the operation fails.",
"name":"The name of the cluster.",
"timeout_mins":"The time-out period for creating a cluster.",
"cluster_type":"The type of the cluster. Set the value to ManagedKubernetes.",
"profile":"The identifier of an edge cluster. Default value: Edge.",
"region_id":"The ID of the region.",
"vpcid": "VPC ID",
"vswitch_ids":"The IDs of VSwitches. Specify one to three VSwitch IDs.",
"container_cidr":"The CIDR block of pods.",
"service_cidr": "The CIDR block of services.",
"cloud_monitor_flags":"Specifies whether to install the Cloud Monitor agent.",
"login_password":"The SSH logon password. You must set login_password or key_pair.",
"key_pair":"The name of the key pair. You must set key_pair or login_password.",
"worker_instance_types":"The ECS instance types of worker nodes.", ",
"worker_system_disk_category":"The system disk type of worker nodes.",
"worker_system_disk_size":"The system disk size of a worker node.",
"worker_data_disk":"Specifies whether to mount data disks to worker nodes. Valid values: true and false.",
"worker_data_disk_category":"The data disk type of worker nodes.",
"worker_data_disk_size":"The size of a data disk.",
"num_of_nodes":"The number of worker nodes.",
"snat_entry":"Specifies whether to configure Source Network Address Translation (SNAT).",
"endpoint_public_access":"Specifies whether to enable Internet access to the endpoint of the cluster.",
"tags":"The tags of the cluster. This parameter must be an array.",
}
```

|Parameter|Type|Required|Description|
|---------|----|--------|-----------|
|cluster\_type|string|Yes|The type of the cluster.|
|key\_pair|string|Yes|The name of the key pair. You must set key\_pair or login\_password.|
|login\_password|string|Yes|The SSH logon password. The password must be 8 to 30 characters in length and contain a minimum of three of the following character types: uppercase letters, lowercase letters, digits, and special characters. You must set login\_password or key\_pair.|
|name|string|Yes|The name of the cluster. The name can contain uppercase letters, lowercase letters, digits, and hyphens \(-\).|
|num\_of\_nodes|int|Yes|The number of worker nodes. Valid values: 0 to 300.|
|profile|string|Yes|The identifier of an edge cluster. Default value: Edge.|
|region\_id|string|Yes|The ID of the region where the cluster is deployed.|
|snat\_entry|bool|Yes|Specifies whether to configure SNAT. If the virtual private cloud \(VPC\) is automatically created by the system, set the value to true. If an existing VPC is specified, set the value based on whether the VPC has Internet access.|
|vswitch\_ids|list|Yes|The IDs of VSwitches. Specify one to three VSwitch IDs.|
|worker\_system\_disk\_category|string|Yes|The system disk type of worker nodes.|
|worker\_system\_disk\_size|int|Yes|The system disk size of a worker node.|
|container\_cidr|string|No|The CIDR block of pods. This CIDR block cannot overlap with the CIDR block of the VPC. If the VPC is automatically created by the system, the CIDR block of pods is set to 172.16.0.0/16.|
|cloud\_monitor\_flags|bool|No|Specifies whether to install the Cloud Monitor agent. **Note:** If you enable Log Service or Cloud Monitor, Container Service for Kubernetes \(ACK\) automatically purchases an Elastic Compute Service \(ECS\) instance and installs the logging or monitoring component on the instance. Therefore, you must configure the logon information and ECS instance type of the worker node. |
|disable\_rollback|bool|No|Specifies whether to retain all resources if the operation fails. Valid values: -   true: retains the resources.
-   false: releases the resources.

We recommend that you do not use the false value.|
|proxy\_mode|string|No|The kube-proxy mode. Valid values: iptables and ipvs. Default value: iptables.|
|endpoint\_public\_access|bool|No|Specifies whether to enable access to the API server over the Internet. Valid values: -   true: enables access to the API server over the Internet. Default value: true.
-   false: disables access to the API server over the Internet. The API server allows only access over an internal network.

**Note:** To create a managed edge cluster, enable access to the API server over the Internet. This allows edge nodes to connect to the management nodes in the cloud over the Internet. |
|service\_cidr|string|No|The CIDR block of services. This CIDR block cannot overlap with the CIDR block of the VPC or pods. If the VPC is automatically created by the system, the CIDR block of services is set to 172.19.0.0/20.|
|addons|Array|No|The add-ons to be installed for the cluster.

-   Configure the following parameters for add-ons:
    -   name: Required. The name of the add-on.
    -   config: Optional. If you do not set this parameter, no add-on configurations are required.
    -   disabled: specifies whether to disable default installation. This parameter is optional.
-   Network add-on: Required. You can select `Flannel` only. Key-value pair "name":"flannel" must be included in the value of addons.
-   Log Service: Optional. If Log Service is disabled, the cluster audit feature is unavailable. You can select the following types of Log Service projects:
    -   If you select an existing project, key-value pairs `"name":"logtail-ds-docker", "name":"alibaba-log-controller", "IngressDashboardEnabled":"false", and "sls_project_name":"your_sls_project_name"` must be included in the value of addons.
    -   If you create a project, key-value pairs `"name":"logtail-ds-docker", "name":"alibaba-log-controller", and "IngressDashboardEnabled":"false"` must be included in the value of addons. |
|tags|list|No|The tags of the cluster. -   key: the key of the tag.
-   value: the value of the tag. |
|timeout\_mins|int|No|The time-out period in minutes during which a cluster creation operation must be completed. Default value: 60.|
|vpcid|string|No|The ID of the VPC. You can leave this parameter empty. If you do not set this parameter, the system automatically creates a VPC that uses the CIDR block 192.168.0.0/16. You must specify both the vpcid and vswitch\_ids parameters or leave both parameters empty.|
|worker\_data\_disk|string|No|Specifies whether to mount data disks to worker nodes. Valid values: -   true: mounts data disks.
-   false: does not mount data disks. |
|worker\_data\_disk\_category|int|No|The type of data disks.|
|worker\_data\_disk\_size|string|No|The size of a data disk.|

## Response information

Response line

```
HTTP/1.1 202 Accepted
```

Operation-specific response parameters

No operation-specific response parameters are used. For more information, see [Common parameters](/intl.en-US/API Reference/Common parameters.md).

Response body

```
{
"cluster_id":"string",
"request_id":"string",
"task_id":"string"
}
```

## Examples

Sample requests

```
POST /clusters HTTP/1.1
<Common request parameters>
{
"name":"test",
"cluster_type":"ManagedKubernetes",
"profile":"Edge",
"disable_rollback":true,
"timeout_mins":60,
"region_id":"cn-beijing",
"snat_entry":true,
"cloud_monitor_flags":true,
"endpoint_public_access":true,
"tags":[],
"worker_instance_types":["ecs.hfc5.xlarge"],
"num_of_nodes":1,
"worker_system_disk_category":"cloud_efficiency",
"vpcid":"vpc-2zegvl5eta******",
"container_cidr":"172.20.0.0/16",
"service_cidr":"172.21.0.0/20",
"vswitch_ids":["vsw-2ze48rkq464rsdts1****"],
"login_password":"test@19****"
}
```

Sample success responses

```
HTTP/1.1 202 Accepted
<Common response parameters>
{
    "cluster_id": "cb95aa626a47740afbf6aa099b65****",
    "RequestId": "687C5BAA-D103-4993-884B-C35E4314****",
    "task_id": "T-5a54309c80282e39ea00****"
}
```

