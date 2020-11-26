# Create a managed cluster

You can call CreateCluster to create a managed cluster and add a specified number of nodes to the cluster.

## Request information

Request line

```
POST /clusters HTTP/1.1 
```

Operation-specific request parameters

This operation uses only common request parameters. For more information, see [Common parameters](/intl.en-US/API Reference/Common parameters.md).

Request body

**Note:** You must set parameters correctly. Otherwise, you cannot create a cluster. If you create a cluster in the Container Service for Kubernetes \(ACK\) console, you can click **Generate API Request Parameters** on the Create an ACK Cluster page to view all parameters. For more information, see [Generate API request parameters](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Generate API request parameters.md).

```
{
"disable_rollback":"Specifies whether to retain all resources if the operation fails.",
"name":"The name of the cluster.",
"timeout_mins":"The time-out period for creating a cluster.",
"cluster_type":"The type of the cluster. Set the value to ManagedKubernetes.",
"region_id":"The ID of the region.",
"vpcid": "VPC ID",
"vswitch_ids":"The IDs of VSwitches. Specify one to three VSwitch IDs.",
"container_cidr":"The CIDR block of pods.",
"service_cidr": "The CIDR block of services.",
"cloud_monitor_flags":"Specifies whether to install the Cloud Monitor agent.",
"login_password":"The SSH logon password. You must set login_password or key_pair.",
"key_pair":"The name of the key pair. You must set key_pair or login_password.",
"worker_instance_charge_type":"The billing method of worker nodes. Valid values: PrePaid and PostPaid.",
"worker_period_unit":"The unit of the subscription duration of worker nodes. Valid values: Month and Year. This parameter takes effect only if worker_instance_charge_type is set to PrePaid.",
"worker_period":"The subscription duration of worker nodes. This parameter takes effect only if worker_instance_charge_type is set to PrePaid.",
"worker_auto_renew":"Specifies whether to enable auto renewal for worker nodes. Valid values: true and false.",
"worker_auto_renew_period":"The auto renewal period for worker nodes.",
"worker_instance_types":"The ECS instance types of worker nodes.", ",
"worker_system_disk_category":"The system disk type of worker nodes.",
"worker_system_disk_size":"The system disk size of a worker node.",
"worker_data_disk":"Specifies whether to mount data disks to worker nodes. Valid values: true and false.",
"worker_data_disks":"The data disk configurations of worker nodes.",
"num_of_nodes":"The number of worker nodes.",
"snat_entry":"Specifies whether to configure Source Network Address Translation (SNAT).",
"endpoint_public_access":"Specifies whether to enable Internet access to the endpoint of the cluster.",
"proxy_mode":"The kube-proxy mode. Valid values: iptables and ipvs.",
"addons":"The add-ons to be installed for the cluster. This parameter must be an array.", 
"tags":"The tags of the cluster. This parameter must be an array.",
"security_group_id":"The ID of the security group. Set security_group_id or is_enterprise_security_group.",
"is_enterprise_security_group":"Specifies whether to enable automatic creation of an advanced security group. Set the value to true if you leave security_group_id empty.",
"taints":"The taints to be added to nodes in the cluster. This parameter must be an array."
"cpu_policy":"The CPU management policy. Valid values: static and none.",
"runtime":"The runtime of containers. Default value: docker. You must specify the name and version of the runtime. For example, "runtime":{"name":"docker","version":"19.03.5"}.",
"platform":"The specific operating system of the nodes that run pods.",
"os_type":"The operating system type of the nodes that run pods. For example, Linux and Windows.",
"node_cidr_mask":"The prefix length of the node CIDR block.",
"kubernetes_version":"The version of the cluster. By default, the value is the latest version.",
"deletion_protection":"Specifies whether to enable cluster deletion protection. If this option is enabled, the cluster cannot be deleted by operations in the console or API operations."
}
```

|Parameter|Type|Required|Description|
|---------|----|--------|-----------|
|cluster\_type|string|Yes|The type of the cluster. Valid values:-   Kubernetes: a dedicated cluster.
-   ManagedKubernetes: a standard managed cluster or a managed edge cluster.
-   Ask: a standard serverless cluster. |
|key\_pair|string|Yes|The name of the key pair. You must set key\_pair or login\_password.|
|login\_password|string|Yes|The SSH logon password. The password must be 8 to 30 characters in length and contain a minimum of three of the following character types: uppercase letters, lowercase letters, digits, and special characters. You must set login\_password or key\_pair.|
|name|string|Yes|The name of the cluster. The name can contain uppercase letters, lowercase letters, digits, and hyphens \(-\).|
|num\_of\_nodes|int|Yes|The number of worker nodes. Valid values: 0 to 100.|
|region\_id|string|Yes|The ID of the region where the cluster is deployed.|
|snat\_entry|bool|Yes|Specifies whether to configure SNAT. If the virtual private cloud \(VPC\) is automatically created by the system, set the value to true. If an existing VPC is specified, set the value based on whether the VPC has Internet access.|
|vswitch\_ids|list|Yes|The IDs of VSwitches. Specify one to three VSwitch IDs.|
|worker\_system\_disk\_category|string|Yes|The system disk type of worker nodes.|
|worker\_system\_disk\_size|int|Yes|The system disk size of a worker node.|
|addons|list|No|The add-ons installed for the cluster. -   Configure the following parameters for add-ons:
    -   name: Required. The name of the add-on.
    -   version: Optional. By default, the value is the latest version.
    -   config: Optional.
-   Network add-on: Select Flannel or Terway.
-   Log Service: Optional. If Log Service is disabled, the cluster audit feature is unavailable.
-   Ingress: By default, the nginx-ingress-controller component is installed. |
|container\_cidr|string|No|The CIDR block of pods. This CIDR block cannot overlap with the CIDR block of the VPC. If the VPC is automatically created by the system, the CIDR block of pods is set to 172.16.0.0/16.|
|cloud\_monitor\_flags|bool|No|Specifies whether to install the Cloud Monitor agent.|
|disable\_rollback|bool|No|Specifies whether to retain all resources if the operation fails. Valid values: -   true: retains the resources.
-   false: releases the resources.

We recommend that you do not use the false value.|
|public\_slb|bool|No|Specifies whether to enable access to the API server over the Internet. Valid values: -   true: enables access to the API server over the Internet. Default value: true.
-   false: disables access to the API server over the Internet. The API server allows only access over an internal network.

**Note:** The parameter is obsolete and replaced by parameter endpoint\_public\_access. |
|proxy\_mode|string|No|The kube-proxy mode. Valid values: iptables and ipvs. Default value: iptables.|
|endpoint\_public\_access|bool|No|Specifies whether to enable access to the API server over the Internet. Valid values: -   true: enables access to the API server over the Internet. Default value: true.
-   false: disables access to the API server over the Internet. The API server allows only access over an internal network. |
|security\_group\_id|string|No|The ID of the security group to which the ECS instances in the cluster belong. You must set security\_group\_id or is\_enterprise\_security\_group.|
|is\_enterprise\_security\_group|bool|No|Specifies whether to enable automatic creation of an advanced security group. Set the value to true if you leave security\_group\_id empty.|
|service\_cidr|string|No|The CIDR block of services. This CIDR block cannot overlap with the CIDR block of the VPC or pods. If the VPC is automatically created by the system, the CIDR block of services is set to 172.19.0.0/20.|
|addons|Array|No|The add-ons to be installed for the cluster.

-   Configure the following parameters for add-ons:
    -   name: Required. The name of the add-on.
    -   config: Optional. If you do not set this parameter, no add-on configurations are required.
    -   disabled: specifies whether to disable default installation. This parameter is optional.
-   Network add-on: Required. Select Flannel or Terway.
    -   If you select Flannel, key-value pair `"name":"flannel"` must be included in the value of addons.
    -   If you select Terway, key-value pair `"name":"terway-eniip"` must be included in the value of addons.
-   Volume plug-in: Required. Select `CSI` or `FlexVolume`.
    -   If you select CSI, key-value pairs `"name":"csi-plugin" and "name":"csi-provisioner"` must be included in the value of addons.
    -   If you select FlexVolume, key-value pair `"name":"flexvolume"` must be included in the value of addons.
-   Log Service: Optional. If Log Service is disabled, the cluster audit feature is unavailable. You can select the following types of Log Service projects:
    -   If you select an existing project, key-value pairs `"name":"logtail-ds", "IngressDashboardEnabled":"true", and "sls_project_name":"your_sls_project_name"` must be included in the value of addons.
    -   If you create a project, key-value pairs `"name":"logtail-ds" and "IngressDashboardEnabled":"true"` must be included in the value of addons.
-   Ingress: Optional. By default, the nginx-ingress-controller component is installed.
    -   If you select to install nginx-ingress-controller and enable access over the Internet, key-value pairs `"name":"nginx-ingress-controller" and "IngressSlbNetworkType":"internet"`must be included in the value of addons.
    -   If you do not want to install nginx-ingress-controller, key-value pairs`"name":"nginx-ingress-controller" and "disabled":"true"`must be included in the value of addons.
-   Event Center: Optional. By default, the Event Center feature is enabled. The Event Center feature allows you to store and query Kubernetes events. It also allows you to configure alerts for the events. By default, events are retained for 90 days free of charge. For more information, see [Create and use a Kubernetes event center](/intl.en-US/Application/K8s Event Center/Create and use a Kubernetes event center.md).

If you enable the Event Center feature, key-value pairs "name":"ack-node-problem-detector" and "sls\_project\_name":"" must be included in the value of addons. |
|tags|list|No|The tags of the cluster. -   key: the key of the tag.
-   value: the value of the tag. |
|taints|list|No|The taints that are added to nodes to ensure appropriate scheduling of pods. If a pod has a toleration that matches the taint on a node, this pod can be scheduled to the node.|
|timeout\_mins|int|No|The time-out period in minutes during which a cluster creation operation must be completed. Default value: 60.|
|vpcid|string|Yes|The ID of the VPC. **Note:** You must specify both the vpcid and vswitch\_ids parameters. |
|worker\_auto\_renew|bool|No|Specifies whether to enable auto renewal for worker nodes. Valid values: -   true: enables auto renewal.
-   false: disables auto renewal. |
|worker\_auto\_renew\_period|int|No|The auto renewal period for worker nodes. This parameter takes effect and is required only if worker\_instance\_charge\_type is set to PrePaid. If worker\_period\_unit is set to Month, valid values of worker\_auto\_renew\_period include 1, 2, 3, 6, and 12.|
|worker\_data\_disk|string|No|Specifies whether to mount data disks to worker nodes. Valid values: -   true: mounts data disks.
-   false: does not mount data disks. |
|worker\_data\_disks|list|No|The data disk configurations of worker nodes, such as the disk type and disk size. This parameter takes effect only if worker\_data\_disk is set to true. -   category: the type of data disks. Valid values:
    -   cloud: basic disks.
    -   cloud\_efficiency: ultra disks.
    -   cloud\_ssd: SSDs.
-   size: the size of a data disk. Unit: GiB. |
|worker\_data\_disk\_category|int|No|The type of data disks. **Note:** This parameter is obsolete and replaced by the category parameter in worker\_data\_disks. |
|worker\_data\_disk\_size|string|No|The size of a data disk. **Note:** This parameter is obsolete and replaced by the size parameter in worker\_data\_disks. |
|worker\_instance\_charge\_type|string|No|The billing method of worker nodes. Valid values: -   PrePaid: subscription.
-   PostPaid: pay-as-you-go. |
|worker\_period|int|No|The subscription duration of worker nodes. This parameter takes effect and is required only if worker\_instance\_charge\_type is set to PrePaid. If `worker_period_unit` is set to Month, valid values of worker\_period include 1, 2, 3, 6, and 12.|
|worker\_period\_unit|string|No|The unit of the subscription duration. This parameter is required if worker\_instance\_charge\_type is set to PrePaid. **Note:** A value of Month specifies that the subscription duration is measured in months. |
|worker\_instance\_types|list|Yes|The ECS instance types of worker nodes. For more information, see [Instance families](/intl.en-US/Instance/Instance families.md).|
|cpu\_policy|string|No|The CPU management policy. For Kubernetes 1.12.6 and later, valid values of cpu\_policy include static and none. Default value: none.|
|runtime|json|No|The runtime of containers. You must specify the name and version of the runtime: -   name: the name of the container runtime. For example, docker and Sandboxed-Container.runv. Default value: docker.
-   version: the version of the container runtime. |
|platform|string|No|The specific operating system of the nodes that run pods. For example, CentOS, AliyunLinux, Windows, and WindowsCore.|
|os\_type|string|No|The operating system type of the nodes that run pods. For example, Linux and Windows.|
|node\_cidr\_mask|string|No|The prefix length of the node CIDR block. This parameter specifies the maximum number of pods on a node. If you set the value to 24, each node can run a maximum of 256 pods. If you set the value to 25, each node can run a maximum of 128 pods. Calculation formula: Maximum number of pods on each node = 2^\(32 - node\_cidr\_mask\).|
|kubernetes\_version|string|No|The version of the cluster. By default, the value is the latest version.|
|deletion\_protection|bool|No|Specifies whether to enable cluster deletion protection. If this option is enabled, the cluster cannot be deleted by operations in the console or API operations.|

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
    "name":"amk-cluster",
    "cluster_type":"ManagedKubernetes",
    "disable_rollback":true,
    "timeout_mins":60,
    "kubernetes_version":"1.16.9-aliyun.1",
    "region_id":"cn-beijing",
    "snat_entry":true,
    "cloud_monitor_flags":true,
    "endpoint_public_access":true,
    "deletion_protection":true,
    "node_cidr_mask":"26",
    "proxy_mode":"ipvs",
    "tags":[
        {
            "key":"tier",
            "value":"backend"
        }
    ],
    "addons":[{"name":"flannel"},{"name":"csi-plugin"},{"name":"csi-provisioner"},{"name":"logtail-ds","config":"{\"IngressDashboardEnabled\":\"true\"}"},{"name":"ack-node-problem-detector","config":"{\"sls_project_name\":\"\"}"},{"name":"nginx-ingress-controller","config":"{\"IngressSlbNetworkType\":\"internet\"}"},{"name":"arms-prometheus"}],
    "os_type":"Linux",
    "platform":"CentOS",
    "runtime":{
        "name":"docker",
        "version":"19.03.5"
    },
    "worker_instance_types":[
        "ecs.i2.2xlarge"
    ],
    "num_of_nodes":3,
    "worker_system_disk_category":"cloud_efficiency",
    "worker_system_disk_size":120,
    "worker_data_disks":[
        {
            "category":"cloud_efficiency",
            "size":"40",
            "encrypted":"true",
            "auto_snapshot_policy_id":""
        }
    ],
    "worker_instance_charge_type":"PrePaid",
    "worker_period_unit":"Month",
    "worker_period":1,
    "worker_auto_renew":true,
    "worker_auto_renew_period":1,
    "vpcid":"vpc-2zemm8mo5rmdppgqm****",
    "container_cidr":"172.20.0.0/16",
    "service_cidr":"172.21.0.0/20",
    "vswitch_ids":[
        "vsw-2zej67xyhh61oqn7i****"
    ],
    "login_password":"Hello1234",
    "logging_type":"SLS",
    "cpu_policy":"none",
    "taints":[
        {
            "key":"key1",
            "value":"value1",
            "effect":"NoSchedule"
        }
    ],
    "security_group_id":"sg-2zeg3u73kkhtixda****"
}
```

Sample success responses

```
HTTP/1.1 202 Accepted
<Common response parameters>
{
    "cluster_id": "cb95aa626a47740afbf6aa099b65****",
    "RequestId": "687C5BAA-D103-4993-884B-C35E4314A1E1",
    "task_id": "T-5a54309c80282e39ea00002f"
}
```

