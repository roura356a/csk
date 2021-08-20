# Create a dedicated Kubernetes cluster

You can call the CreateCluster operation to create a standard dedicated Kubernetes cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=CreateCluster&type=ROA&version=2015-12-15)

## Request syntax

```
POST /clusters HTTP/1.1 
Content-Type:application/json
{
  "name" : "String",
  "region_id" : "String",
  "cluster_type" : "String",
  "kubernetes_version" : "String",
  "runtime" : {
    "name" : "String",
    "version" : "String"
  },
  "vpcid" : "String",
  "pod_vswitch_ids" : [ "String" ],
  "container_cidr" : "String",
  "service_cidr" : "String",
  "security_group_id" : "String",
  "is_enterprise_security_group" : Boolean,
  "snat_entry" : Boolean,
  "endpoint_public_access" : Boolean,
  "ssh_flags" : Boolean,
  "timezone" : "String",
  "node_cidr_mask" : "String",
  "user_ca" : "String",
  "user_data" : "String",
  "cluster_domain" : "String",
  "node_name_mode" : "String",
  "custom_san" : "String",
  "service_account_issuer" : "String",
  "api_audiences" : "String",
  "image_id" : "String",
  "rds_instances" : [ "String" ],
  "tags" : [ {
    "key" : "String",
    "value" : "String"
  } ],
  "addons" : [ {
    "name" : "String",
    "config" : "String",
    "disabled" : Boolean
  } ],
  "taints" : [ {
    "key" : "String",
    "value" : "String",
    "effect" : "String"
  } ],
  "cloud_monitor_flags" : Boolean,
  "platform" : "String",
  "os_type" : "String",
  "soc_enabled" : Boolean,
  "cis_enabled" : Boolean,
  "cpu_policy" : "String",
  "proxy_mode" : "String",
  "node_port_range" : "String",
  "key_pair" : "String",
  "login_password" : "String",
  "master_count" : Long,
  "master_vswitch_ids" : [ "String" ],
  "master_instance_types" : [ "String" ],
  "master_system_disk_category" : "String",
  "master_system_disk_size" : Long,
  "master_system_disk_snapshot_policy_id" : "String",
  "master_instance_charge_type" : "String",
  "master_period_unit" : "String",
  "master_period" : Long,
  "master_auto_renew" : Boolean,
  "master_auto_renew_period" : Long,
  "num_of_nodes" : Long,
  "vswitch_ids" : [ "String" ],
  "worker_vswitch_ids" : [ "String" ],
  "worker_instance_types" : [ "String" ],
  "worker_system_disk_category" : "String",
  "worker_system_disk_size" : Long,
  "worker_system_disk_snapshot_policy_id" : "String",
  "worker_data_disks" : [ {
    "category" : "String",
    "size" : Long,
    "encrypted" : "String",
    "auto_snapshot_policy_id" : "String"
  } ],
  "worker_instance_charge_type" : "String",
  "worker_period_unit" : "String",
  "worker_period" : Long,
  "worker_auto_renew" : Boolean,
  "worker_auto_renew_period" : Long,
  "instances" : [ "String" ],
  "format_disk" : Boolean,
  "keep_instance_name" : Boolean,
  "deletion_protection" : Boolean,
  "disable_rollback" : Boolean,
  "timeout_mins" : Long
}
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|name|String|Yes|cluster-demo|The name of the cluster.

The name must be 1 to 63 characters in length, and can contain digits, letters, and hyphens \(-\). It cannot start with a hyphen \(-\). |
|region\_id|String|Yes|cn-beijing|The ID of the region where you want to deploy the cluster. |
|cluster\_type|String|Yes|Kubernetes|The type of the cluster. Valid values:

-   `Kubernetes`: dedicated Kubernetes cluster.
-   `ManagedKubernetes`: standard managed Kubernetes cluster and managed edge Kubernetes cluster.
-   `Ask`: serverless Kubernetes \(ASK\) cluster.
-   `ExternalKubernetes`: external cluster that is registered to Container Service for Kubernetes \(ACK\). |
|kubernetes\_version|String|No|1.16.9-aliyun.1|The Kubernetes version of the cluster. The Kubernetes versions provided by ACK are consistent with the open source Kubernetes versions. We recommend that you select the latest Kubernetes version. If you do not specify this parameter, the latest Kubernetes version is used.

You can create clusters of the latest two Kubernetes versions in the ACK console. You can create clusters of earlier Kubernetes versions by calling API operations. For more information about the Kubernetes versions supported by ACK, see [t1960241.dita\#task\_1960241](/intl.en-US/Release notes/Kubernetes release notes/Overview of Kubernetes versions supported by ACK.md). |
|runtime|[runtime](/intl.en-US/API Reference/Commonly used parameters.md)|No|\{"name": "docker", "version": "19.03.5"\}|The container runtime of the cluster. The following runtimes are supported: `containerd`, `Docker`, and `Sandboxed-Container`. The default runtime is `Docker`. You must specify the name and version of the container runtime. -   `name`: the name of the container runtime.
-   `version`: the version of the container runtime.

For more information about how to select a proper container runtime, see [t1879872.dita\#task\_2455499](/intl.en-US/User Guide for Kubernetes Clusters/Sandboxed-Container management/Comparison of Docker, containerd, and Sandboxed-Container.md). |
|vpcid|String|Yes|vpc-2zeik9h3ahvv2zz95\*\*\*\*|The virtual private cloud \(VPC\) where the cluster is deployed. You must specify a VPC when you create the cluster. |
|pod\_vswitch\_ids|Array of String|No|vsw-2ze97jwri7cei0mpw\*\*\*\*|The list of pod vSwitches. For each vSwitch that is allocated to nodes, you must specify at least one pod vSwitch in the same zone as the vSwitch. The pod vSwitches cannot be the same as the node `vSwitches`. We recommend that you set the mask length of the CIDR block to a value no greater than 19 for the pod vSwitches.

**Note:** The `pod_vswitch_ids` parameter is required when the Terway network plug-in is selected for the cluster. |
|container\_cidr|String|Yes|172.20.0.0/16|The CIDR block of pods. This CIDR block cannot overlap with the CIDR block of the VPC in which the cluster is deployed. If the VPC is automatically created by the system, the CIDR block of pods is set to 172.16.0.0/16 by default.

**Note:**

-   This parameter is required if the cluster uses the Flannel plug-in.
-   This parameter is not required if the cluster uses the Terway plug-in. |
|service\_cidr|String|Yes|172.21.0.0/20|The CIDR block of Services. Valid values: 10.0.0.0/16-24, 172.16-31.0.0/16-24, and 192.168.0.0/16-24.

The CIDR block of Services cannot overlap with the CIDR block of the VPC \(10.1.0.0/21\) and the CIDR blocks of existing clusters in the VPC. You cannot modify the CIDR block of Services after the cluster is created.

By default, the CIDR block of Services is set to 172.19.0.0/20. |
|security\_group\_id|String|No|sg-bp1bdue0qc1g7k\*\*\*\*|The ID of the existing security group that is specified for the cluster. You must set this parameter or the `is_enterprise_security_group` parameter. Nodes in the cluster are automatically added to the specified security group. |
|is\_enterprise\_security\_group|Boolean|No|true|Specifies whether to create an advanced security group. This parameter takes effect only if `security_group_id` is left empty.

**Note:** To use a basic security group, make sure that the sum of the number of nodes in the cluster and the number of pods that use Terway does not exceed 2,000. Therefore, we recommend that you specify an advanced security group for a cluster that has Terway installed.

-   `true`: creates an advanced security group.
-   `false`: does not create an advanced security group.

Default value: `true`. |
|snat\_entry|Boolean|No|true|Specifies whether to configure Source Network Address Translation \(SNAT\) rules for the VPC where your cluster is deployed. Valid values:

-   true: automatically creates a Network Address Translation \(NAT\) gateway and configures SNAT rules. Set this parameter to `true` if nodes and applications in the cluster need to access the Internet.
-   false: does not create a NAT gateway or configure SNAT rules. In this case, nodes and applications in the cluster cannot access the Internet.

**Note:** If this feature is disabled when you create the cluster, you can also manually enable this feature after you create the cluster. For more information, see [t1936294.dita\#task\_1936294](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/Enable an existing ACK cluster to access the Internet by using SNAT.md).

Default value: true. |
|endpoint\_public\_access|Boolean|No|true|Specifies whether to enable Internet access for the cluster. You can use an elastic IP address \(EIP\) to expose the API server. This way, you can access the cluster over the Internet.

-   `true`: enables Internet access.
-   `false`: disables Internet access. If you set this parameter to false, the API server cannot be accessed over the Internet.

Default value: `false`. |
|ssh\_flags|Boolean|No|true|Specifies whether to enable SSH logon over the Internet. If this parameter is set to true, you can log on to master nodes in a dedicated Kubernetes cluster over the Internet. This parameter does not take effect in managed Kubernetes clusters.

-   `true`: enables SSH logon over the Internet.
-   `false`: disables SSH logon over the Internet.

Default value: `false`. |
|timezone|String|No|Asia/Shanghai|The time zone of the cluster. |
|node\_cidr\_mask|String|No|25|The maximum number of IP addresses that can be assigned to nodes. This number is determined by the specified pod CIDR block. This parameter takes effect only if the cluster uses the Flannel plug-in.

Default value: `26`. |
|user\_ca|String|No|-----BEGIN CERTIFICATE-----\*\*\*\*|The custom Certificate Authority \(CA\) certificate for the cluster. |
|user\_data|String|No|IyEvdXNyL2Jpbi9iYXNoCmVjaG8gIkhlbGxvIEFD\*\*\*\*|The user-defined data. For more information, see [t9660.dita\#concept\_fgf\_tjn\_xdb](/intl.en-US/Instance/Manage instance configurations/Manage instance user data/Overview of ECS instance user data.md). |
|cluster\_domain|String|No|cluster.local|The domain name of the cluster.

The domain name consists of one or more parts that are separated by periods \(.\).Each part cannot exceed 63 characters in length, and can contain lowercase letters, digits, and hyphens \(-\). Each part must start and end with a lowercase letter or digit. |
|node\_name\_mode|String|No|aliyun.com00055test|Specifies a custom node name.

A node name consists of a prefix, an IP substring, and a suffix.

-   The prefix and suffix can contain one or more parts that are separated by periods \(.\).Each part can contain lowercase letters, digits, and hyphens \(-\), and must start and end with a digit or lowercase letter.
-   The IP substring length specifies the number of digits to be truncated from the end of the node IP address. The IP substring length ranges from 5 to 12.

For example, if the node IP address is 192.168.0.55, the prefix is aliyun.com, the IP substring length is 5, and the suffix is test, the node name is aliyun.com00055test. |
|custom\_san|String|No|cs.aliyun.com|Specifies custom subject alternative names \(SANs\) for the API server certificate to accept requests from specified IP addresses or domain names. Multiple IP addresses and domain names are separated by commas \(,\). |
|service\_account\_issuer|String|No|kubernetes.default.svc|A service account is used to provide an identity for pods when they communicate with the `API server`. `service-account-issuer` is the issuer of the `service account token`, which corresponds to the `iss` field in the `token payload`.

For more information about `service accounts`, see [t1881266.dita\#task\_2460323](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Infrastructure security/Enable service account token volume projection.md). |
|api\_audiences|String|No|kubernetes.default.svc|A service account is used to provide an identity for pods when they communicate with the `API server`. `api-audiences` are valid identifiers of `tokens`. Audiences are used to validate `tokens` at the `API server` side. Separate multiple `audiences` with commas \(,\).

For more information about `service accounts`, see [t1881266.dita\#task\_2460323](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Infrastructure security/Enable service account token volume projection.md). |
|image\_id|String|No|m-bp16z7xko3vvv8gt\*\*\*\*|Specifies a custom image for nodes. By default, the system image is used. You can select a custom image to replace the default image. For more information, see [t1849273.dita\#task\_2362493](/intl.en-US/Best Practices/Migrate a self-built Kubernetes cluster to Container Service for Kubernetes/Use a custom image to create an ACK cluster.md). |
|rds\_instances|Array of String|No|rm-2zev748xi27xc\*\*\*\*|The names of ApsaraDB RDS instances. |
|tags|Array of [tag](/intl.en-US/API Reference/Commonly used parameters.md)|No| |The labels to be added to nodes. Labeling rules:

-   Each label consists of a case-sensitive key-value pair. You can add up to 20 labels.
-   When you add a label, you must specify a unique key but you can leave the value empty. A key cannot exceed 64 characters in length and a value cannot exceed 128 characters in length. Keys and values cannot start with aliyun, acs:, https://, or http://. For more information, see [Labels and Selectors](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set). |
|addons|Array of [addon](/intl.en-US/API Reference/Commonly used parameters.md)|No| |The list of components to be installed. When you create a cluster, you can set the `addons` parameter to install specific components.

**Network plug-in**: required. Supported network plug-ins are Flannel and Terway. Select one of the plug-ins for the cluster.

-   Specify the Flannel plug-in in the following format: \[\{"name":"flannel","config":""\}\].
-   Specify the Terway plug-in in the following format: \[\{"name": "terway-eniip","config": ""\}\].

**Volume plug-in**: Required. Supported volume plug-ins are `CSI` and `FlexVolume`.

-   Specify the `CSI` plug-in in the following format: \[\{"name":"csi-plugin","config": ""\},\{"name": "csi-provisioner","config": ""\}\].
-   Specify the `FlexVolume` plug-in in the following format: \[\{"name": "flexvolume","config": ""\}\].

**Log Service component**: optional. We recommend that you enable Log Service. If Log Service is disabled, you cannot use the cluster auditing feature.

-   To use an existing `Log Service project`, specify the component in the following format: \[\{"name": "logtail-ds","config": "\{\\"IngressDashboardEnabled\\":\\"true\\",\\"sls\_project\_name\\":\\"your\_sls\_project\_name\\"\}"\}\].
-   To create a `Log Service project`, specify the component in the following format: \[\{"name": "logtail-ds","config": "\{\\"IngressDashboardEnabled\\":\\"true\\"\}"\}\].

**Ingress controller**: optional. By default, the `nginx-ingress-controller` component is installed for dedicated Kubernetes clusters.

-   To install nginx-ingress-controller and enable Internet access, specify the component in the following format: \[\{"name":"nginx-ingress-controller","config":"\{\\"IngressSlbNetworkType\\":\\"internet\\"\}"\}\].
-   To disable the system to automatically install nginx-ingress-controller, specify the component in the following format: \[\{"name": "nginx-ingress-controller","config": "","disabled": true\}\].

**Event center**: optional. By default, the event center feature is enabled.

The event center feature allows you to log Kubernetes events, query events, and generate alerts. Logstores that are associated with the Kubernetes event center are free of charge for the first 90 days. For more information, see [t1857672.dita\#task\_2389213](/intl.en-US/Application/K8s Event Center/Create and use a Kubernetes event center.md).

Enable the ack-node-problem-detector component in the following format: \[\{"name":"ack-node-problem-detector","config":"\{\\"sls\_project\_name\\":\\"your\_sls\_project\_name\\"\}"\}\]. |
|taints|Array of [taint](/intl.en-US/API Reference/Commonly used parameters.md)|No| |The taints that you want to add to nodes. Taints are added to nodes to prevent pods from being scheduled to inappropriate nodes. However, toleration rules allow pods to be scheduled to nodes with matching taints. For more information, see [taint-and-toleration](https://kubernetes.io/zh/docs/concepts/scheduling-eviction/taint-and-toleration/). |
|cloud\_monitor\_flags|Boolean|No|true|Specifies whether to install the CloudMonitor agent. Valid values:

-   `true`: installs the CloudMonitor agent.
-   `false`: does not install the CloudMonitor agent.

Default value: `false`. |
|platform|String|No|CentOS|The release version of the operating system. Valid values:

-   CentOS
-   AliyunLinux
-   QbootAliyunLinux
-   Qboot
-   Windows
-   WindowsCore

Default value: `AliyunLinux`. |
|os\_type|String|No|Linux|The type of operating system. Valid values:

-   Windows
-   Linux

Default value: `Linux`. |
|soc\_enabled|Boolean|No|false|Valid values:

-   `true`: enables reinforcement based on classified protection.
-   `false`: disables reinforcement based on classified protection.

Default value: `false`. |
|cis\_enabled|Boolean|No|false|Specifies whether to enable Center for Internet Security \(CIS\) reinforcement. For more information, see [CIS reinforcement](~~223744~~).

Valid values:

-   `true`: enables CIS reinforcement.
-   `true`: enables CIS reinforcement.

Default value: `false`. |
|cpu\_policy|String|No|none|The CPU policy. The following policies are supported if the Kubernetes version is 1.12.6 or later.

-   `static`: This policy allows pods with specific resource characteristics on the node to be granted with enhanced CPU affinity and exclusivity.
-   `none`: This policy indicates that the default CPU affinity is used.

Default value: `none`. |
|proxy\_mode|String|No|ipvs|The kube-proxy mode.

-   `iptables`: iptables is a kube-proxy mode. It uses iptables rules to conduct Service discovery and load balancing. The performance of this mode is limited by the size of the cluster. This mode is suitable for clusters that run a small number of Services.
-   `IPVS` is a high-performance kube-proxy mode. It uses Linux IP Virtual Server \(IPVS\) to conduct Service discovery and load balancing. This mode is suitable for clusters that run a large number of Services. We recommend that you use this mode in scenarios where high-performance load balancing is required.

Default value: `ipvs`. |
|node\_port\_range|String|No|30000~32767|The node port range. Valid values: 30000 to 65535.

Default value: `30000-32767`. |
|key\_pair|String|Yes|secrity-key|The name of the key pair. You must set this parameter or the `login_password` parameter. |
|login\_password|String|Yes|Hello@1234|The password for SSH logon. You must set this parameter or the `key_pair` parameter. The password must be 8 to 30 characters in length, and must contain at least three of the following character types: uppercase letters, lowercase letters, digits, and special characters. |
|master\_count|Long|No|3|The number of master nodes that you want to create. Valid values: `3` and `5`.

Default value: `3`. |
|master\_vswitch\_ids|Array of String|Yes|vsw-2ze3ds0mdip0hdz8i\*\*\*\*|The IDs of vSwitches. |
|master\_instance\_types|Array of String|Yes|ecs.n4.xlarge|The instance types of master nodes. For more information about Elastic Compute Service \(ECS\) instance types, see [Instance families](/intl.en-US/Instance/Instance families.md). |
|master\_system\_disk\_category|String|Yes|cloud\_ssd|The type of system disk that is specified for master nodes. Valid values:

-   `cloud_efficiency`: ultra disk.
-   `cloud_ssd`: standard SSD.
-   `cloud_essd`: enhanced SSD.

Default value: `cloud_ssd`. The default value may vary in different zones. |
|master\_system\_disk\_size|Long|Yes|120|The size of the system disk that is specified for master nodes. Valid values: 40 to 500. Unit: GiB.

Default value: `120`. |
|master\_system\_disk\_snapshot\_policy\_id|String|No|sp-2zej1nogjvovnz4z\*\*\*\*|The ID of the automatic snapshot policy that is used by the system disk specified for master nodes. |
|master\_instance\_charge\_type|String|No|PrePaid|The billing method of master nodes. Valid values:

-   `PrePaid`: subscription.
-   `PostPaid`: pay-as-you-go.

Default value: `PostPaid`. |
|master\_period\_unit|String|No|Month|The billing cycle of master nodes. This parameter is required if master\_instance\_charge\_type is set to `PrePaid`.

Set the value to `Month`. Master nodes are billed only on a monthly basis. |
|master\_period|Long|No|1|The subscription duration of master nodes. This parameter takes effect and is required only if `master_instance_charge_type` is set to `PrePaid`.

Valid values: 1, 2, 3, 6, 12, 24, 36, 48, and 60.

Default value: 1 |
|master\_auto\_renew|Boolean|No|true|Specifies whether to enable auto-renewal for master nodes. This parameter takes effect only if `master_instance_charge_type` is set to `PrePaid`. Valid values:

-   `true`: enables auto-renewal.
-   `false`: disables auto-renewal.

Default value: `true`. |
|master\_auto\_renew\_period|Long|No|1|The cycle of auto-renewal. This parameter takes effect and is required only if the subscription billing method is selected for master nodes.

Valid values: 1, 2, 3, 6, and 12.

Default value: 1. |
|num\_of\_nodes|Long|Yes|3|The number of worker nodes to be created. Valid values: 0 to 100. |
|worker\_vswitch\_ids|Array of String|Yes|vsw-2ze3ds0mdip0hdz8i\*\*\*\*|The list of vSwitch IDs. You can specify 1 to 20 vSwitches. We recommend that you select vSwitches in different zones to ensure high availability. |
|worker\_instance\_types|Array of String|Yes|ecs.n4.large|The instance types of worker nodes. You must specify at least one instance type. For more information, see [t9548.dita\#concept\_sx4\_lxv\_tdb](/intl.en-US/Instance/Instance families.md).

**Note:** The instance types are listed in descending order of priority. If worker nodes fail to be created based on the instance type of the highest priority, the system attempts to create worker nodes by using the instance type of the next highest priority. |
|worker\_system\_disk\_category|String|Yes|cloud\_efficiency|The type of system disk that is specified for worker nodes. Valid values:

-   `cloud_efficiency`: ultra disk.
-   `cloud_ssd`: standard SSD.

Default value: `cloud_ssd`. |
|worker\_system\_disk\_size|Long|Yes|120|The size of the system disk that is specified for worker nodes. Unit: GiB.

Valid values: 40 to 500.

The value of this parameter must be at least 40 and greater than or equal to the size of the image.

Default value: 120. |
|worker\_system\_disk\_snapshot\_policy\_id|String|No|sp-2zej1nogjvovnz4z\*\*\*\*|The ID of the automatic snapshot policy that is used by the system disk specified for worker nodes. |
|worker\_data\_disks|Array of [data\_disk](/intl.en-US/API Reference/Commonly used parameters.md)|No| |The configuration of the data disk that is mounted to worker nodes. The configuration includes disk type and disk size. |
|worker\_instance\_charge\_type|String|No|PrePaid|The billing method of worker nodes. Valid values:

-   `PrePaid`: subscription
-   `PostPaid`: pay-as-you-go

Default value: PostPaid. |
|worker\_period\_unit|String|No|Month|The billing cycle of worker nodes. This parameter is required if worker\_instance\_charge\_type is set to `PrePaid`.

Set the value to `Month`. Worker nodes are billed only on a monthly basis. |
|worker\_period|Long|No|1|The subscription duration of worker nodes. This parameter takes effect and is required only if `worker_instance_charge_type` is set to `PrePaid`.

Valid values: 1, 2, 3, 6, 12, 24, 36, 48, and 60.

Default value: 1. |
|worker\_auto\_renew|Boolean|No|true|Specifies whether to enable auto-renewal for worker nodes. This parameter takes effect only if `worker_instance_charge_type` is set to `PrePaid`. Valid values:

-   `true`: enables auto-renewal.
-   `false`: disables auto-renewal.

Default value: `true`. |
|worker\_auto\_renew\_period|Long|No|1|The cycle of auto-renewal. This parameter takes effect and is required only if the subscription billing method is selected for worker nodes.

Valid values: 1, 2, 3, 6, and 12. |
|instances|Array of String|No|i-2ze4zxnm36vq00xn\*\*\*\*|The names of instances. |
|format\_disk|Boolean|No|false|Specifies whether to mount a data disk to nodes that are created based on existing ECS instances. Valid values:

-   `true`: stores the data of containers and images on a data disk. The original data on the disk will be overwritten. Back up data before you mount the disk.
-   `false`: does not store the data of containers and images on a data disk.

Default value: `false`.

How to mount a data disk:

-   If the ECS instances have data disks mounted and the file system of the last data disk is not initialized, the system automatically formats the data disk to ext4. Then, the system mounts the data disk to /var/lib/docker and /var/lib/kubelet.
-   The system does not create or mount a new data disk if no data disk has been mounted to the ECS instances. |
|keep\_instance\_name|Boolean|No|true|Specifies whether to retain the names of existing ECS instances that are used in the cluster.

-   `true`: retains the names.
-   `false`: does not retain the names. The new names are assigned by the system.

Default value: `true`. |
|deletion\_protection|Boolean|No|true|Indicates whether deletion protection is enabled for the cluster. After deletion protection is enabled, the cluster cannot be deleted in the console or by calling API operations. Valid values:

-   `true`: enables deletion protection for the cluster. This way, the cluster cannot be deleted in the ACK console or by calling API operations.
-   `false`: disables deletion protection for the cluster. This way, the cluster can be deleted in the ACK console or by calling API operations.

Default value: `false`. |
|disable\_rollback|Boolean|No|true|Specifies whether to perform a rollback when the cluster fails to be created. Valid values:

-   `true`: performs a rollback when the cluster fails to be created.
-   `false`: does not perform a rollback when the cluster fails to be created.

Default value: `true`. |
|timeout\_mins|Long|No|60|Specifies the timeout period of cluster creation. Unit: minutes.

Default value: `60`. |

## Response syntax

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
|cluster\_id|String|cb95aa626a47740afbf6aa099b650\*\*\*\*|The ID of the ACK cluster. |
|request\_id|String|687C5BAA-D103-4993-884B-C35E4314A1E1|The ID of the request. |
|task\_id|String|T-5a54309c80282e39ea00002f|The ID of the task. |

## Examples

Sample requests

```
POST /clusters
{
    "cluster_type":"Kubernetes",
    "name":"dedicated Kubernetes cluster",
    "region_id":"cn-zhangjiakou",
    "disable_rollback":true,
    "timeout_mins":60,
    "kubernetes_version":"1.18.8-aliyun.1",
    "snat_entry":true,
    "endpoint_public_access":true,
    "ssh_flags":true,               // Specifies whether master nodes in the cluster can be accessed by using SSH. 
    "cloud_monitor_flags":true,
    "deletion_protection":true,
    "node_cidr_mask":"26",
    "user_ca":"-----BEGIN CERTIFICATE-----\n***", // The custom CA certificate. 
    "proxy_mode":"ipvs",
    "timezone":"Asia/Shanghai",
    "tags":[
        {
            "key":"tag-k",
            "value":"tag-v"
        }
    ],
    "addons":[
        {
            "name":"flannel"
        },
        {
            "name":"arms-prometheus"
        },
        {
            "name":"csi-plugin"
        },
        {
            "name":"csi-provisioner"
        },
        {
            "name":"logtail-ds",
            "config":"{"IngressDashboardEnabled":"true"}"
        },
        {
            "name":"ack-node-problem-detector",
            "config":"{"sls_project_name":""}"
        },
        {
            "name":"nginx-ingress-controller",
            "config":"{"IngressSlbNetworkType":"internet"}"
        }
    ],
    "os_type":"Linux",
    "soc_enabled" : false,
    "cis_enabled" : false,
    "platform":"AliyunLinux",
    "user_data":"IyEvdXNyL2Jpbi9iYXNoCmVjaG8gIkhlbGxvIEFDSyEi",
    "node_port_range":"30000-32767",      // The node port range. Valid values: 30000 to 65535. 
    "login_password":"Hello1234",
    "key_pair": "sin-name",
    "master_instance_charge_type":"PrePaid", // The billing method of master nodes. 
    "worker_instance_charge_type":"PrePaid",
    "master_period":1,                       // The subscription duration of master nodes. 
    "worker_period":1,
    "master_period_unit":"Month",            // The billing cycle of master nodes. 
    "worker_period_unit":"Month",
    "master_auto_renew":true,                // Specifies whether auto-renewal is enabled for master nodes. 
    "master_auto_renew_period":1,            // The duration of auto-renewal. 
    "worker_auto_renew":true,
    "worker_auto_renew_period":1,
    "cpu_policy":"none",
    "cluster_domain":"cluster.local",
    "node_name_mode":"customized,aliyun,5,test",
    "custom_san":"cs.aliyuncs.com",
    "service_account_issuer":"kubernetes.default.svc",
    "api_audiences":"kubernetes.default.svc",
    "taints":[
        {
            "key":"key",
            "value":"value",
            "effect":"NoSchedule"
        }
    ],
    "master_count":3,                 // The number of master nodes. Valid values: 3 and 5.
    "master_vswitch_ids":[            // The IDs of the vSwitches that are specified for master nodes. 
        "vsw-8vbmoffowsztjaawjtyzo",
        "vsw-8vbmoffowsztjaawjtyzo",
        "vsw-8vbmoffowsztjaawjtyzo"
    ],
    "master_instance_types":[         // The instance types of master nodes. 
        "ecs.c6.large",
        "ecs.c6.large",
        "ecs.c6.large"
    ],
    "master_system_disk_category":"cloud_essd",   // The type of system disk that is specified for master nodes. 
    "master_system_disk_size":120,                // The size of the system disk that is specified for master nodes. Valid values: 40 to 500. 
    "runtime":{
        "name":"docker",
        "version":"19.03.5"
    },
    "worker_instance_types":[
        "ecs.g5.large"
    ],
    "num_of_nodes":3,
    "worker_system_disk_category":"cloud_essd",
    "worker_system_disk_size":120,
    "worker_system_disk_performance_level":"PL1",
    "vpcid":"vpc-8vbh3b9a2f38urhlsw9jh",
    "worker_vswitch_ids":[
        "vsw-8vbmoffowsztjaawjtyzo"
    ],
    "security_group_id":"sg-8vb7grbyvlb10j0ih46n",
    "is_enterprise_security_group":true,
    "container_cidr":"172.20.0.0/16",
    "service_cidr":"172.21.0.0/20",
    "rds_instances": ["rm-xx","rm-xx"],                
    "image_id":"CentOS-xxx",                          
    "pod_vswitch_ids":[                                 
        "vsw-8vbo5fwyqiw0bbtlq0mc9"
    ],
    "instances": [                
        "i-dewgagxdfa****",
        "i-3kjaf9q43l****"
    ],
    "format_disk": false,        
    "keep_instance_name": true    
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

