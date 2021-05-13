# Create a standard managed Kubernetes cluster that supports sandboxed

Creates a standard managed Kubernetes cluster that supports sandboxed containers.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=CreateCluster&type=ROA&version=2015-12-15)

## Request structure

```
POST /clusters HTTP/1.1
Content-Type:application/json
{
  "addons" : [ {
    "name" : "String",
    "config" : "String",
    "disabled" : Boolean
  } ],
  "cloud_monitor_flags" : Boolean,
  "cluster_type" : "String",
  "container_cidr" : "String",
  "cpu_policy" : "String",
  "deletion_protection" : Boolean,
  "disable_rollback" : Boolean,
  "endpoint_public_access" : Boolean,
  "is_enterprise_security_group" : Boolean,
  "key_pair" : "String",
  "kubernetes_version" : "String",
  "login_password" : "String",
  "name" : "String",
  "node_cidr_mask" : "String",
  "node_port_range" : "String",
  "num_of_nodes" : Long,
  "pod_vswitch_ids" : [ "String" ],
  "proxy_mode" : "String",
  "region_id" : "String",
  "runtime" : {
    "name" : "String",
    "version" : "String"
  },
  "service_cidr" : "String",
  "security_group_id" : "String",
  "snat_entry" : Boolean,
  "ssh_flags" : Boolean,
  "tags" : [ {
    "key" : "String",
    "value" : "String"
  } ],
  "taints" : [ {
    "key" : "String",
    "value" : "String",
    "effect" : "String"
  } ],
  "timeout_mins" : Long,
  "user_data" : "String",
  "vpcid" : "String",
  "vswitch_ids" : [ "String" ],
  "worker_auto_renew" : Boolean,
  "worker_auto_renew_period" : Long,
  "worker_data_disks" : [ {
    "category" : "String",
    "size" : Long,
    "encrypted" : "String",
    "auto_snapshot_policy_id" : "String"
  } ],
  "worker_vswitch_ids" : [ "String" ],
  "worker_instance_types" : [ "String" ],
  "worker_system_disk_category" : "String",
  "worker_system_disk_size" : Long,
  "worker_instance_charge_type" : "String",
  "worker_period_unit" : "String",
  "worker_period" : Long,
  "zone_id" : "String"
}
            
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|addons|Array|Yes|\[\{"name": "terway-eniip","config": ""\}, \{"name": "logtail-ds","config": "\{\\"IngressDashboardEnabled\\":\\"true\\",\\"sls\_project\_name\\":\\"your\_sls\_project\_name\\"\}"\}, \{"name":"nginx-ingress-controller","config":"\{\\"IngressSlbNetworkType\\":\\"internet\\"\}"\}\]|The list of add-ons to be installed.-   Parameter description:
    -   `name`: This parameter is required.
    -   `config`: This parameter is optional. If this parameter is left empty, it indicates that no configurations are required.
    -   `disabled`: specifies whether to disable automatic installation. This parameter is optional.
-   Network plug-in: Required. Supported network plug-ins are Flannel and Terway. Select one of the plug-ins for the cluster.
    -   Specify the Flannel plug-in in the following format: `[{"name":"flannel","config":""}]`.
    -   Specify the Terway plug-in in the following format: `[{"name": "terway-eniip","config": "\"IPVlan\":\"false\""}]`.
-   Volume plug-in: Required. Supported volume plug-ins are CSI and FlexVolume.
    -   Specify the `CSI` plug-in in the following format: `[{"name":"csi-plugin","config": ""},{"name": "csi-provisioner","config": ""}]`.
    -   Specify the `FlexVolume` plug-in in the following format: `[{"name": "flexvolume","config": ""}]`.
-   Log Service component: Optional.

**Note:** If Log Service is disabled, you cannot use the cluster auditing feature.

    -   To use an existing Log Service project, specify the component in the following format: `[{"name": "logtail-ds","config": "{"IngressDashboardEnabled":"true","sls_project_name":"your_sls_project_name"}"}]`.
    -   To create a Log Service project, specify the component in the following format: `[{"name": "logtail-ds","config": "{"IngressDashboardEnabled":"true"}"}]`.
-   Ingress controller: Optional. By default, the nginx-ingress-controller component is installed.
    -   To install nginx-ingress-controller and enable Internet access, specify the component in the following format: `[{"name":"nginx-ingress-controller","config":"{"IngressSlbNetworkType":"internet"}"}]`.
    -   If you do not want to install nginx-ingress-controller, specify the component in the following format: `[{"name": "nginx-ingress-controller","config": "","disabled": true}]`.
-   Event center: Optional. By default, the event center feature is enabled. The event center feature allows you to log Kubernetes events, query events, and raise alerts. Logstores that are associated with the Kubernetes event center are free of charge for the first 90 days. For more information, see [Create and use a Kubernetes event center](/intl.en-US/Application/K8s Event Center/Create and use a Kubernetes event center.md).

Enable the ack-node-problem-detector component in the following format: `[{"name":"ack-node-problem-detector","config":"{\"sls_project_name\":\" your_sls_project_name\"}"}]`. |
|cloud\_monitor\_flags|Boolean|No|true|Specifies whether to install the Cloud Monitor agent. Valid values:-   `true`: installs the Cloud Monitor agent.
-   `false`: does not install the Cloud Monitor agent.

Default value: `false`. |
|cluster\_type|String|Yes|ManagedKubernetes|The type of cluster. If you want to create a standard managed Kubernetes cluster that supports sandboxed containers, set the value to `ManagedKubernetes`.|
|container\_cidr|String|No|172.20.0.0/16|The CIDR block of pods. This CIDR block cannot overlap with the CIDR block of the virtual private cloud \(VPC\) in which the cluster is deployed. If the VPC is automatically created by the system, the CIDR block of pods is set to 172.16.0.0/16 by default. This parameter is required if the cluster uses the Flannel plug-in.|
|cpu\_policy|String|No|none|The CPU policy. The following policies are supported if the cluster version is 1.12.6 or later.-   `static`: allows pods with specific resource characteristics on the node to be granted with enhanced CPU affinity and exclusivity.
-   `none`: This policy indicates that the default CPU affinity is used.

Default value: `none`. |
|deletion\_protection|Boolean|No|true|Specifies whether to enable deletion protection for the cluster. After deletion protection is enabled, the cluster cannot be deleted in the console or by calling API operations. Valid values:-   `true`: enables deletion protection for the cluster.
-   `false`: disables deletion protection for the cluster.

Default value: `false`. |
|disable\_rollback|Boolean|No|true|Specifies whether to perform a rollback when the cluster fails to be created. Valid values:-   `true`: performs a rollback when the cluster fails to be created.
-   `false`: does not perform a rollback when the cluster fails to be created.

Default value: `false`. |
|endpoint\_public\_access|Boolean|No|true|Specifies whether to enable Internet access for the API server. Valid values:-   `true`: enables Internet access for the API server.
-   `false`: disables Internet access for the API server. The API server is accessible only within the internal network.

Default value: `true`. |
|is\_enterprise\_security\_group|Boolean|No|true|Specifies whether to create an advanced security group. This parameter takes effect only if security\_group\_id is left empty. You must specify an advanced security group for a cluster that has Terway installed. -   `true`: creates an advanced security group.
-   `false`: does not create an advanced security group.

Default value: `false`.|
|key\_pair|String|Yes|secrity-key|The name of the key pair. You must set this parameter or the `login_password` parameter.|
|kubernetes\_version|String|No|1.16.9-aliyun.1|The version of the cluster. The cluster versions provided by Container Service for Kubernetes \(ACK\) are consistent with the open source versions. We recommend that you select the latest version. If you do not specify this parameter, the latest version is used. You can create clusters of the latest two versions in the ACK console. You can create ACK clusters of earlier versions by calling API operations. For more information about the Kubernetes versions supported by ACK, see [Release notes](/intl.en-US/Release notes/Kubernetes release notes/Release notes.md).|
|login\_password|String|Yes|Hello@1234|The password for Secure Shell \(SSH\) logon. You must set this parameter or the `key_pair` parameter. The password must be 8 to 30 characters in length, and must contain at least three of the following character types: uppercase letters, lowercase letters, digits, and special characters.|
|name|String|Yes|cluster-demo|The name of the cluster. The name must be 1 to 63 characters in length, and can contain digits, letters, and hyphens \(-\). It cannot start with a hyphen.|
|node\_cidr\_mask|String|No|25|The maximum number of IP addresses that can be assigned to each node. This number is determined by the specified pod CIDR block. This parameter takes effect only if the cluster uses the Flannel plug-in. Default value: 25. |
|node\_port\_range|String|No|30000~32767|The node port range. Valid values: 30000 to 65535.|
|num\_of\_nodes|Long|Yes|3|The number of worker nodes to be created. Valid values: 0 to 100.|
|pod\_vswitch\_ids|Array of String|No|vsw-2ze97jwri7cei0mpw\*\*\*\*|The list of pod vSwitches. **Note:** The `pod_vswitch_ids` parameter is required when the Terway network plug-in is selected for the cluster.

For each vSwitch that is allocated to nodes, you must specify at least one pod vSwitch in the same zone. The pod vSwitches cannot be the same as the node `vSwitches`.|
|proxy\_mode|String|No|ipvs|The kube-proxy mode. Valid values: `iptables` and `ipvs`. Default value: `ipvs`. |
|region\_id|String|Yes|cn-beijing|The ID of the region where you want to deploy the cluster.|
|runtime|Object|Yes| |The container runtime. **Note:** This parameter is required. |
|name|Object|Yes|Sandboxed-Container.runv|The version of the container runtime. The name of the container runtime. Valid values:-   `Sandboxed-Container.runv`: Sandboxed-Container.
-   `docker`: Docker.

Default value: `docker`.

**Note:** Set the value to `Sandboxed-Container.runv` if you want to create a cluster that supports sandboxed containers. |
|version|Object|Yes|2.1.0|The version of the container runtime. By default, the latest version is used. For more information about the release notes on Sandboxed-Container, see [Sandboxed-Container release notes](/intl.en-US/Release notes/Release notes for Runtime/Sandboxed-Container release notes.md).|
|security\_group\_id|String|No|sg-bp1bdue0qc1g7k\*\*\*\*|The ID of the existing security group that is specified for the cluster. You must set this parameter or the `is_enterprise_security_group` parameter. Nodes in the cluster are automatically added to the specified security group.|
|service\_cidr|String|Yes|172.21.0.0/20|The CIDR block of Services. This CIDR block cannot overlap with the CIDR block of pods or the CIDR block of the VPC in which the cluster is deployed. If the VPC is automatically created by the system, the CIDR block of Services is set to 172.19.0.0/20 by default.|
|snat\_entry|Boolean|No|true|Specifies whether to configure Source Network Address Translation \(SNAT\) rules for the VPC where your cluster is deployed.-   If the VPC supports Internet access, set the value to `false`.
-   If the VPC does not support Internet access, valid values are:
    -   `true`: configures SNAT rules. This enables Internet access for the cluster.
    -   `false`: does not configure SNAT rules. In this case, the cluster cannot access the Internet.

If your applications deployed in the cluster need to access the Internet, we recommend that you set the value to `true`.

Default value: `false`. |
|ssh\_flags|Boolean|No|true|Specifies whether to enable SSH logon over the Internet. Valid values:-   `true`: enables SSH logon over the Internet.
-   `false`: disables SSH logon over the Internet.

Default value: `false`.|
|tags|Array|No| |The labels to be added to the cluster.|
|key|String|No|env|The key of the label.|
|value|String|No|prod|The value of the label.|
|taints|Array|No| |The taints to be added to nodes. Taints are added to nodes to prevent pods from being scheduled to inappropriate nodes. However, toleration rules allow pods to be scheduled to nodes with matching taints. For more information, see [taint-and-toleration](https://kubernetes.io/zh/docs/concepts/scheduling-eviction/taint-and-toleration/).|
|effect|String|No|NoSchedule|The scheduling policy. Valid values:-   NoSchedule: A pod cannot be scheduled to a node with taints.
-   NoExecute: If a taint is added to a node, all pods that do not tolerate the taint are immediately evicted from the node.
-   PreferNoSchedule: a soft version of NoSchedule. The system attempts not to schedule a pod to a node with taints. |
|key|String|No|disk\_type|The key of the taint.|
|value|String|No|sshd|The value of the taint.|
|timeout\_mins|Long|No|60|The timeout period of cluster creation. Default value: 60. |
|user\_data|String|No|IyEvdXNyL2Jpbi9iYXNoCmVjaG8gIkhlbGxvIEFD\*\*\*\*|The user-defined data. For more information, see [Prepare user data](/intl.en-US/Instance/Manage instances/User data/Prepare user data.md).|
|vpcid|String|Yes|vpc-2zeik9h3ahvv2zz95\*\*\*\*|The ID of the VPC where the cluster is deployed.|
|vswitch\_ids|Array of String|Yes|vsw-2ze48rkq464rsdts1\*\*\*\*"|The IDs of vSwitches.|
|worker\_auto\_renew|Boolean|No|true|Specifies whether to enable auto-renewal for worker nodes. This parameter takes effect only if `worker_instance_charge_type` is set to `PrePaid`. Valid values:-   true: enables auto-renewal.
-   false: disables auto-renewal.

Default value: true.|
|worker\_auto\_renew\_period|Long|No|1|The cycle of auto-renewal. This parameter takes effect and is required only if the subscription billing method is selected for worker nodes. Valid values: 1, 2, 3, 6, and 12.|
|worker\_data\_disks|Array|Yes| |The configurations of the data disks that are mounted to worker nodes. Each configuration includes disk type and disk size.|
|auto\_snapshot\_policy\_id|String|No|sp-bp14j6w7ss6ozz\*\*\*\*|The ID of the automatic snapshot policy.|
|category|String|No|cloud\_ssd|The type of data disk that is mounted to worker nodes. Valid values:-   `cloud_efficiency`: ultra disk.
-   `cloud_ssd`: standard SSD.
-   `cloud`: basic disk.

Default value: `cloud_efficiency`.|
|encrypted|String|No|false|Specifies whether to encrypt the data disk. Valid values:-   `true`: encrypts the data disk.
-   `false`: does not encrypt the data disk.

Default value: `false`. |
|size|String|Yes|200|The size of the data disk. Unit: GiB. Valid values:-   cloud\_efficiency: 20 to 32768.
-   cloud\_ssd: 20 to 32768.
-   cloud: 5 to 2000.

**Note:** You must mount at least one data disk to nodes that run sandboxed containers. The data disk must be at least 200 GiB in size. |
|worker\_instance\_charge\_type|String|Yes|PrePaid|The billing method of worker nodes. Valid values:-   `PrePaid`: subscription.
-   `PostPaid`: pay-as-you-go.

Default value: PostPaid.|
|worker\_instance\_types|Array of String|Yes|ecs.ebmg5s.24xlarge|The instance types of worker nodes. **Note:** To create a cluster that supports sandboxed containers, you must select **ECS Bare Metal instances**. |
|worker\_period|Long|No|1|The subscription duration of worker nodes. This parameter takes effect and is required only if `worker_instance_charge_type` is set to `PrePaid`. Valid values: 1, 2, 3, 6, 12, 24, 36, 48, and 60. Default value: 1. |
|worker\_period\_unit|String|No|Month|The billing cycle of worker nodes. This parameter is required if worker\_instance\_charge\_type is set to `PrePaid`. Set the value to `Month`. Worker nodes are billed only on a monthly basis.|
|worker\_system\_disk\_category|String|No|cloud\_efficiency|The type of system disk that is specified for worker nodes. Valid values:-   `cloud_efficiency`: ultra disk.
-   `cloud_ssd`: standard SSD.

Default value: `cloud_ssd`. |
|worker\_system\_disk\_size|Long|No|120|The size of the system disk that is specified for worker nodes. Unit: GiB.

Valid values: 40 to 500.

The value of this parameter must be at least 40 and greater than or equal to the size of the image.

Default value: `120`. |
|worker\_vswitch\_ids|Array of String|No|vsw-2ze3ds0mdip0hdz8i\*\*\*\*|The list of vSwitches that are specified for nodes. Each node is allocated a vSwitch.|
|zone\_id|String|No|cn-beijing-b|The ID of the zone to which the cluster belongs.|

## Response structure

```
HTTP/1.1 200
Content-Type:application/json
{
  "cluster_id" : "String",
  "request_id" : "String",
  "task_id" : "String"
}
```

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|cluster\_id|String|cb95aa626a47740afbf6aa099b650\*\*\*\*|The ID of the cluster.|
|request\_id|String|687C5BAA-D103-4993-884B-C35E4314A1E1|The ID of the request.|
|task\_id|String|T-5a54309c80282e39ea00002f|The ID of the task.|

## Example 1: Create a standard managed Kubernetes cluster that supports sandboxed containers and uses the Flannel plug-in

Sample requests

```
POST /clusters
Common request headers
{
  "name": "webService",
  "cluster_type": "ManagedKubernetes",
  "disable_rollback": true,
  "timeout_mins": 60,
  "kubernetes_version": "1.18.8-aliyun.1",
  "region_id": "cn-hangzhou",
  "snat_entry": true,
  "cloud_monitor_flags": true,
  "endpoint_public_access": false,
  "deletion_protection": false,
  "node_cidr_mask": "26",
  "proxy_mode": "ipvs",
  "tags": [],
  "timezone": "Asia/Shanghai",
  "addons": [{
    "name": "flannel"
  }, {
    "name": "sandboxed-container-controller"
  }, {
    "name": "csi-plugin"
  }, {
    "name": "csi-provisioner"
  }, {
    "name": "logtail-ds",
    "config": "{\"IngressDashboardEnabled\":\"true\"}"
  }, {
    "name": "ack-node-problem-detector",
    "config": "{\"sls_project_name\":\"\"}"
  }, {
    "name": "nginx-ingress-controller",
    "config": "{\"IngressSlbNetworkType\":\"internet\"}"
  }, {
    "name": "arms-prometheus"
  }],
  "runtime": {
    "name": "Sandboxed-Container.runv",
    "version": "2.1.0"
  },
  "worker_instance_types": ["ecs.ebmc5s.24xlarge"],
  "num_of_nodes": 3,
  "worker_system_disk_category": "cloud_essd",
  "worker_system_disk_size": 120,
  "worker_data_disks": [{
    "category": "cloud_efficiency",
    "size": "200",
    "encrypted": "false",
    "auto_snapshot_policy_id": ""
  }],
  "worker_instance_charge_type": "PostPaid",
  "vpcid": "vpc-bp1gxh70jnkl12vq27jg7",
  "container_cidr": "172.23.0.0/16",
  "service_cidr": "172.21.0.0/20",
  "vswitch_ids": ["vsw-bp1hl2o4i9z7sbmy*****"],
  "login_password": "Hello1234!",
  "logging_type": "SLS",
  "cpu_policy": "none",
  "is_enterprise_security_group": true
}
```

Sample success responses

`XML` format

```
<cluster_id>cb95aa626a47740afbf6aa099b650****</cluster_id>
<task_id>T-5a54309c80282e39ea00002f</task_id>
<request_id>687C5BAA-D103-4993-884B-C35E4314A1E1</request_id>
```

`JSON` format

```
{
    "cluster_id": "cb95aa626a47740afbf6aa099b650****",
    "task_id": "T-5a54309c80282e39ea00002f",
    "request_id": "687C5BAA-D103-4993-884B-C35E4314A1E1"
}
```

## Example 2: Create a standard managed Kubernetes cluster that supports sandboxed containers and uses the Terway plug-in

**Note:** `pod_vswitch_ids` is required if you create a cluster that uses the Terway plug-in.

Sample requests

```
POST /clusters HTTP/1.1
Common request headers
{
  "name": "webService",
  "cluster_type": "ManagedKubernetes",
  "disable_rollback": true,
  "timeout_mins": 60,
  "kubernetes_version": "1.18.8-aliyun.1",
  "region_id": "cn-hangzhou",
  "snat_entry": true,
  "cloud_monitor_flags": true,
  "endpoint_public_access": false,
  "deletion_protection": false,
  "proxy_mode": "ipvs",
  "tags": [],
  "timezone": "Asia/Shanghai",
  "addons": [{
    "name": "terway-eniip",
    "config": "{\"IPVlan\":\"false\",\"NetworkPolicy\":\"false\"}"
  }, {
    "name": "sandboxed-container-controller"
  }, {
    "name": "csi-plugin"
  }, {
    "name": "csi-provisioner"
  }, {
    "name": "logtail-ds",
    "config": "{\"IngressDashboardEnabled\":\"true\"}"
  }, {
    "name": "ack-node-problem-detector",
    "config": "{\"sls_project_name\":\"\"}"
  }, {
    "name": "nginx-ingress-controller",
    "config": "{\"IngressSlbNetworkType\":\"internet\"}"
  }, {
    "name": "arms-prometheus"
  }],
  "pod_vswitch_ids": ["vsw-bp1e5819t8dl8ulcrpgkm"],
  "runtime": {
    "name": "Sandboxed-Container.runv",
    "version": "2.1.0"
  },
  "worker_instance_types": ["ecs.ebmc5s.24xlarge"],
  "num_of_nodes": 3,
  "worker_system_disk_category": "cloud_essd",
  "worker_system_disk_size": 120,
  "worker_data_disks": [{
    "category": "cloud_efficiency",
    "size": "200",
    "encrypted": "false",
    "auto_snapshot_policy_id": ""
  }],
  "worker_instance_charge_type": "PostPaid",
  "vpcid": "vpc-bp1gxh70jnkl12vq27jg7",
  "service_cidr": "172.21.0.0/20",
  "vswitch_ids": ["vsw-bp1hl2o4i9z7sbmy*****"],
  "login_password": "Hello1234!",
  "logging_type": "SLS",
  "cpu_policy": "none",
  "is_enterprise_security_group": true
}
```

Sample success responses

`XML` format

```
<cluster_id>cb95aa626a47740afbf6aa099b650****</cluster_id>
<task_id>T-5a54309c80282e39ea00002f</task_id>
<request_id>687C5BAA-D103-4993-884B-C35E4314A1E1</request_id>
```

`JSON` format

```
{
    "cluster_id": "cb95aa626a47740afbf6aa099b650****",
    "task_id": "T-5a54309c80282e39ea00002f",
    "request_id": "687C5BAA-D103-4993-884B-C35E4314A1E1"
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

