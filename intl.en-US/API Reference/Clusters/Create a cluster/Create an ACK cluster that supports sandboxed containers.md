# Create an ACK cluster that supports sandboxed containers

You can call CreateCluster to create a Container Service for Kubernetes \(ACK\) cluster that supports sandboxed containers.

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
  "name":"The name of the cluster.",
  "cluster_type":"The type of the cluster. Set the value to ManagedKubernetes.",
  "disable_rollback": "true",
  "timeout_mins": "60",
  "region_id":"The ID of the region.",
  "snat_entry":"Specifies whether to configure Source Network Address Translation (SNAT).",
  "cloud_monitor_flags":"Specifies whether to install the Cloud Monitor agent.",
  "public_slb":"Specifies whether to enable access to the endpoint of the cluster over the Internet.",
  "worker_instance_type":"The Elastic Compute Service (ECS) instance type of worker nodes.",
  "num_of_nodes":"The number of worker nodes.",
  "vpcid": "vpc id",
  "vswitch_ids":"The VSwitch IDs of worker nodes.",
  "service_cidr":"The CIDR block of services.",
  "login_password":"The SSH logon password. You must set login_password or key_pair.",
  "key_pair":"The name of the key pair. You must set key_pair or login_password.",
  "worker_system_disk_category":"The system disk type of worker nodes.",
  "worker_system_disk_size":"The system disk size of a worker node.",
  "worker_data_disk_category":"The data disk type of worker nodes.",
  "worker_data_disk_size":"The size of a data disk that a worker node uses.",
  "worker_data_disk":"Specifies whether to mount data disks to worker nodes. Valid values: true and false.",
  "worker_instance_charge_type":"The billing method of worker nodes. Valid values: PrePaid and PostPaid.",
  "worker_period_unit":"The unit of the subscription duration of worker nodes. Valid values: Week and Month. This parameter takes effect only if worker_instance_charge_type is set to PrePaid.",
  "worker_period":"The subscription duration of worker nodes. This parameter takes effect only if worker_instance_charge_type is set to PrePaid.",
  "worker_auto_renew":"Specifies whether to enable auto renewal for worker nodes. Valid values: true and false.",
  "worker_auto_renew_period":"The auto renewal period for worker nodes.",
  "kubernetes_version":"The version of the cluster.",
  "addons": [{"name":"terway-eniip"}],
  "runtime": {"name": "Sandboxed-Container.runv", "version":"1.0.0"},
  "pod_vswitch_ids":"The VSwitch IDs of pods. We recommend that you specify three VSwitches in different zones to ensure high availability."
}
```

|Parameter|Type|Required|Description|
|---------|----|--------|-----------|
|addons|list|Yes|The add-ons to be installed for the cluster. -   Configure the following parameters for add-ons:
    -   name: Required. The name of the add-on.
    -   version: Optional. If you do not set this parameter, the latest version is used.
    -   config: Optional. If you do not set this parameter, no add-on configurations are required.
-   Log Service: Optional.
    -   If Log Service is disabled, the cluster audit feature is unavailable.
    -   To enable Log Service, install the aliyun-log-controller component. In this case, key-value pair `"name":"aliyun-log-controller"` must be included in the value of addons. |
|cluster\_type|string|Yes|The type of the cluster. Set the value to ManagedKubernetes. |
|key\_pair|string|Yes|The name of the key pair. You must set key\_pair or login\_password.|
|kubernetes\_version|string|Yes|The version of the cluster. Set the value to `1.14.6-aliyun.1` or later.|
|login\_password|string|Yes|The SSH logon password. The password must be 8 to 30 characters in length and contain a minimum of three of the following character types: uppercase letters, lowercase letters, digits, and special characters. You must set login\_password or key\_pair.|
|name|string|Yes|The name of the cluster. The name can contain uppercase letters, lowercase letters, digits, and hyphens \(-\).|
|num\_of\_nodes|int|Yes|The number of worker nodes. Valid values: 0 to 100.|
|pod\_vswitch\_ids|list|Yes|The VSwitch IDs of pods. This parameter is required only when you specify terway-eniip as the network add-on.|
|region\_id|string|Yes|The ID of the region where the cluster is deployed.|
|runtime|json|Yes|-   name: the name of the container runtime. Set the value to Sandboxed-Container.runv.
-   version: the version of the container runtime. For example, 1.0.0. You can check the latest version in the ACK console. |
|snat\_entry|bool|Yes|Specifies whether to configure SNAT. -   If the virtual private cloud \(VPC\) supports access over the Internet, set the value to false.
-   If the VPC does not support access over the Internet, valid values of snat\_entry include true and false.
    -   true: configures SNAT. This enables the cluster to access the Internet.
    -   false: does not configure SNAT. This prevents the cluster from accessing the Internet. |
|vpcid|string|Yes|The ID of the VPC. If you do not set this parameter, the system automatically creates a VPC that uses the CIDR block 192.168.0.0/16. **Note:** You must specify both vpcid and vswitch\_ids or leave both parameters empty. |
|vswitch\_ids|list|Yes|The IDs of VSwitches. Specify one to three VSwitch IDs.|
|worker\_data\_disk|bool|Yes|Specifies whether to mount data disks to worker nodes. Set the value to true.|
|worker\_data\_disk\_size|string|Yes|The size of a data disk. Unit: GiB. Set the value to 200 or larger.|
|worker\_instance\_type|string|Yes|The ECS instance type of worker nodes that support sandboxed containers. Valid values:-   ecs.ebmg5s.24xlarge
-   ecs.ebmc5s.24xlarge
-   ecs.ebmgn6i.24xlarge
-   ecs.ebmc6.26xlarge
-   ecs.ebmg6.26xlarge
-   ecs.ebmr6.26xlarge
-   ecs.ebmgn6i.24xlarge
-   ecs.ebmgn6v.24xlarge
-   ecs.ebmhfg6.20xlarge
-   ecs.ebmhfr6.20xlarge |
|worker\_system\_disk\_category|string|Yes|The system disk type of worker nodes.|
|worker\_system\_disk\_size|int|Yes|The system disk size of a worker node. Unit: GiB.|
|cloud\_monitor\_flags|bool|No|Specifies whether to install the Cloud Monitor agent. Valid values: -   true: installs the Cloud Monitor agent.
-   false: does not install the Cloud Monitor agent. |
|disable\_rollback|bool|No|Specifies whether to retain all resources if the operation fails. Valid values: -   true: retains the resources.
-   false: releases the resources.

Default value: true. We recommend that you do not use the false value.|
|endpoint\_public\_access|bool|No|Specifies whether to enable access to the API server over the Internet. Valid values: -   true: enables access to the API server over the Internet. Default value: true.
-   false: disables access to the API server over the Internet. The API server allows only access over an internal network. |
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
    -   If you select to install nginx-ingress-controller and enable access over the Internet, key-value pairs `"name":"nginx-ingress-controller" and "IngressSlbNetworkType":"internet"` must be included in the value of addons.
    -   If you do not want to install nginx-ingress-controller, key-value pairs `"name":"nginx-ingress-controller" and "disabled":"true"` must be included in the value of addons.
-   Event Center: Optional. By default, the Event Center feature is enabled. The Event Center feature allows you to store and query Kubernetes events. It also allows you to configure alerts for the events. By default, events are retained for 90 days free of charge. For more information, see [Create and use a Kubernetes event center](/intl.en-US/Application/K8s Event Center/Create and use a Kubernetes event center.md).

If you enable the Event Center feature, key-value pairs "name":"ack-node-problem-detector" and "sls\_project\_name":"" must be included in the value of addons. |
|tags|list|No|The tags of the cluster. -   key: the key of the tag.
-   value: the value of the tag. |
|timeout\_mins|int|No|The time-out period in minutes during which a cluster creation operation must be completed. Default value: 60.|
|worker\_auto\_renew|bool|No|Specifies whether to enable auto renewal for worker nodes. Valid values: -   true: enables auto renewal.
-   false: disables auto renewal. |
|worker\_auto\_renew\_period|int|No|The auto renewal period for worker nodes. This parameter takes effect only if worker\_instance\_charge\_type is set to PrePaid and worker\_auto\_renew is set to true. -   If `worker_period_unit` is set to Week, valid values of worker\_auto\_renew\_period include 1, 2, and 3.
-   If `worker_period_unit` is set to Month, valid values of worker\_auto\_renew\_period include 1, 2, 3, 6, and 12. |
|worker\_data\_disk\_category|string|No|The type of data disks. This parameter takes effect only if worker\_data\_disk is set to true. Valid values: -   cloud: basic disks.
-   cloud\_efficiency: ultra disks.
-   cloud\_ssd: SSDs. |
|worker\_instance\_charge\_type|string|No|The billing method of worker nodes. Valid values: -   PrePaid: subscription.
-   PostPaid: pay-as-you-go.

Default value: PostPaid.|
|worker\_period|int|No|The subscription duration of worker nodes. This parameter takes effect only if worker\_instance\_charge\_type is set to PrePaid. -   If `worker_period_unit` is set to Week, valid values of worker\_period include 1, 2, 3, and 4.
-   If `worker_period_unit` is set to Month, valid values of worker\_period include 1, 2, 3, 4, 5, 6, 7, 8, 9, 12, 24, 36, 48, and 60. |
|worker\_period\_unit|string|No|The unit of the subscription duration. This parameter is required if worker\_instance\_charge\_type is set to PrePaid. Valid values: -   Week: The subscription duration is measured in weeks.
-   Month: The subscription duration is measured in months. |

## Response information

Response line

```
HTTP/1.1 202 Accepted
```

Operation-specific response parameters

No operation-specific response parameters are used. For more information, see [Common parameters](/intl.en-US/API Reference/Common parameters.md).

|Parameter|Type|Description|
|---------|----|-----------|
|cluster\_id|string|The ID of the cluster.|
|request\_id|string|The ID of the request.|
|task\_id|string|The ID of the task. The task ID is automatically assigned by the system. You can use the task ID to query the task status.|

## Examples

Sample requests

```
POST /clusters HTTP/1.1
<Common request parameters>
{
  "name": "test-sandbox", 
  "cluster_type": "ManagedKubernetes",
  "disable_rollback": true,
  "timeout_mins": 60,
  "region_id": "cn-hangzhou",
  "snat_entry": true,
  "cloud_monitor_flags": false,
  "endpoint_public_access": true,
  "worker_instance_type": "ecs.ebmg5s.24xlarge",
  "num_of_nodes": 2,
  "vpcid": "vpc-bp1iybm49v9jgb50xxxxx",
  "vswitch_ids": ["vsw-bp1ue9z93i9zpcblxxxxx"],
  "service_cidr": "192.168.0.0/16",
  "login_password": "xxxxxxxx",
  "worker_system_disk_category": "cloud_efficiency",
  "worker_system_disk_size": 200,
  "worker_data_disk_category": "cloud_efficiency",
  "worker_data_disk_size": 200,
  "worker_data_disk": true,
  "worker_instance_charge_type": "PostPaid",
  "kubernetes_version": "1.14.6-aliyun.1",
  "addons": [{"name":"terway-eniip"}],
  "runtime": {"name": "Sandboxed-Container.runv", "version": "1.0.0"},
  "pod_vswitch_ids":["vsw-bp18nj6xxfoc2ci2xxxxx"]
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

