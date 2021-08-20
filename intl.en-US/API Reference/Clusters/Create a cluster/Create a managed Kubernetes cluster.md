# Create a managed Kubernetes cluster

You can call the CreateCluster operation to create a managed Kubernetes cluster that contains a specified number of nodes.

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
  "cluster_spec" : "String",
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
  "timezone" : "String",
  "node_cidr_mask" : "String",
  "user_data" : "String",
  "cluster_domain" : "String",
  "node_name_mode" : "String",
  "custom_san" : "String",
  "encryption_provider_key" : "String",
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
  "key_pair" : "String",
  "login_password" : "String",
  "num_of_nodes" : Long,
  "vswitch_ids" : [ "String" ],
  "worker_instance_types" : [ "String" ],
  "worker_system_disk_category" : "String",
  "worker_system_disk_size" : Long,
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
  "controlplane_log_ttl" : "String",
  "controlplane_log_project" : "String",
  "controlplane_log_components" : [ "String" ],
  "deletion_protection" : Boolean,
  "disable_rollback" : Boolean,
  "timeout_mins" : Long
}
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|cluster\_type|String|Yes|ManagedKubernetes|The type of cluster. Set the value to `ManagedKubernetes` to create a standard managed Kubernetes cluster.|
|key\_pair|String|Yes|secrity-key|The name of the key pair. You must set this parameter or the `login_password` parameter.|
|login\_password|String|Yes|Hello@1234|The password for SSH logon. You must set this parameter or the `key_pair` parameter. The password must be 8 to 30 characters in length, and must contain at least three of the following character types: uppercase letters, lowercase letters, digits, and special characters.|
|name|String|Yes|cluster-demo|The name of the ACK cluster.

The name must be 1 to 63 characters in length, and can contain digits, letters, and hyphens \(-\). It cannot start with a hyphen \(-\). |
|num\_of\_nodes|Long|Yes|3|The number of worker nodes that you want to create. Valid values: 0 to 100.|
|region\_id|String|Yes|cn-beijing|The ID of the region where you want to deploy the cluster.|
|snat\_entry|Boolean|No|true|Specifies whether to configure Source Network Address Translation \(SNAT\) rules for the virtual private cloud \(VPC\) where your cluster is deployed.

-   If the VPC supports Internet access, set the value to `false`.
-   If the VPC does not support Internet access, valid values are:
    -   `true`: configures SNAT rules. This enables Internet access for the cluster.
    -   `false`: does not configure SNAT rules. In this case, the cluster cannot access the Internet.

If your applications deployed in the cluster need to access the Internet, we recommend that you set the value to true.

Default value: `false`. |
|vswitch\_ids|Array of String|Yes|\["vsw-2ze48rkq464rsdts1\*\*\*\*"\]|The IDs of the vSwitches. You can specify one to three vSwitches.|
|worker\_system\_disk\_category|String|Yes|cloud\_efficiency|The type of system disk that is specified for worker nodes. Valid values:

-   `cloud_efficiency`: ultra disk.
-   `cloud_ssd`: standard SSD.

Default value: `cloud_ssd`. |
|worker\_system\_disk\_size|Long|Yes|120|The size of the system disk that is specified for worker nodes. Unit: GiB.

Valid values: 40 to 500.

The value of this parameter must be at least 40 and no less than the image size.

Default value: 120. |
|addons|Array of [addon](/intl.en-US/API Reference/Commonly used parameters.md)|No|\[\{"name": "terway-eniip","config": ""\}, \{"name": "logtail-ds","config": "\{\\"IngressDashboardEnabled\\":\\"true\\",\\"sls\_project\_name\\":\\"your\_sls\_project\_name\\"\}"\}, \{"name":"nginx-ingress-controller","config":"\{\\"IngressSlbNetworkType\\":\\"internet\\"\}"\}\]|The components that you want to install in the cluster. Parameter description:

-   `name`: the name of the component. This parameter is required.
-   `config`: This parameter is optional. If this parameter is left empty, it indicates that no configurations are required.
-   `disabled`: specifies whether to disable automatic installation. This parameter is optional.

**Network plug-in**: Required. Supported network plug-ins are Flannel and Terway. Select one of the plug-ins for the cluster.

-   Specify the Flannel plug-in in the following format: \[\{"name":"flannel","config":""\}\].
-   Specify the Terway plug-in in the following format: \[\{"name": "terway-eniip","config": ""\}\].

**Volume plug-in**: Required. Supported volume plug-ins are `CSI` and `FlexVolume`.

-   Specify the `CSI` plug-in in the following format: \[\{"name":"csi-plugin","config": ""\},\{"name": "csi-provisioner","config": ""\}\].
-   Specify the `FlexVolume` plug-in in the following format: \[\{"name": "flexvolume","config": ""\}\].

**Log Service component**: Optional.

**Note:** If Log Service is not enabled, you cannot use the cluster auditing feature.

-   To use an existing Log Service project, specify the component in the following format: \[\{"name": "logtail-ds","config": "\{\\"IngressDashboardEnabled\\":\\"true\\",\\"sls\_project\_name\\":\\"your\_sls\_project\_name\\"\}"\}\]
-   To create a Log Service project, specify the component in the following format: \[\{"name": "logtail-ds","config": "\{\\"IngressDashboardEnabled\\":\\"true\\"\}"\}\].

**Ingress controller**: Optional. By default, the nginx-ingress-controller component is installed in dedicated Kubernetes clusters.

-   To install nginx-ingress-controller and enable Internet access, specify the Ingress controller in the following format: \[\{"name":"nginx-ingress-controller","config":"\{\\"IngressSlbNetworkType\\":\\"internet\\"\}"\}\].
-   To disable the automatic installation of nginx-ingress-controller, specify the component in the following format: \[\{"name": "nginx-ingress-controller","config": "","disabled": true\}\].

**Event center**: Optional. By default, the event center feature is enabled. The event center feature allows you to log Kubernetes events, query events, and raise alerts. Logstores that are associated with the Kubernetes event center are free of charge for the first 90 days. For more information, see [.](/intl.en-US/Application/K8s Event Center/Create and use a Kubernetes event center.md)

To enable the Kubernetes event center, specify the ack-node-problem-detector component in the following format: \[\{"name":"ack-node-problem-detector","config":"\{\\"sls\_project\_name\\":\\"

your\_sls\_project\_name\\"\}"\}\]. |
|cluster\_spec|String|No|ack.pro.small|The type of managed Kubernetes cluster. Valid values:

-   `ack.pro.small`: professional managed Kubernetes cluster.
-   `ack.standard`: standard managed Kubernetes cluster.

Default value: `ack.standard`. If you leave this parameter empty, a standard managed Kubernetes cluster is created.

For more information, see [t1913636.dita\#concept\_2558837](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Introduction to professional managed Kubernetes clusters.md). |
|encryption\_provider\_key|String|No|0fe64791-55eb-4fc7-84c5-c6c7cdca\*\*\*\*|The ID of the key that is managed by Key Management Service \(KMS\). This key is used to encrypt data disks. For more information, see [t22664.dita\#concept\_28935\_zh](/intl.en-US/Product Introduction/What is Key Management Service?.md).

**Note:** The key can be used only in professional managed Kubernetes clusters. |
|container\_cidr|String|Yes|172.20.0.0/16|The CIDR block of pods. This CIDR block cannot overlap with the CIDR block of the VPC in which the cluster is deployed. If the VPC is automatically created by the system, the CIDR block of pods is set to 172.16.0.0/16 by default.

**Note:** This parameter is required if the cluster uses the Flannel plug-in. |
|cloud\_monitor\_flags|Boolean|No|true|Specifies whether to install the CloudMonitor agent. Valid values:

-   `true`: installs the CloudMonitor agent.
-   `false`: does not install the CloudMonitor agent.

Default value: `false`. |
|disable\_rollback|Boolean|No|true|Specifies whether to perform a rollback when the cluster fails to be created. Valid values:

-   `true`: performs a rollback when the cluster fails to be created.
-   `false`: does not perform a rollback when the cluster fails to be created.

Default value: `false`. |
|endpoint\_public\_access|Boolean|No|true|Specifies whether to enable Internet access for the cluster. You can use an elastic IP address \(EIP\) to expose the API server. This way, you can access the cluster over the Internet. Valid values: -   `true`: enables Internet access.
-   `false`: disables Internet access. If you set this parameter to false, the API server cannot be accessed over the Internet.

Default value: `true`. |
|proxy\_mode|String|No|ipvs|The kube-proxy mode. Valid values: `iptables` and `ipvs`. Default value: `ipvs`. |
|security\_group\_id|String|No|sg-bp1bdue0qc1g7k1e\*\*|The ID of the existing security group that is specified for the cluster. You must set this parameter or the `is_enterprise_security_group` parameter. Nodes in the cluster are automatically added to the specified security group. |
|is\_enterprise\_security\_group|Boolean|No|true|Specifies whether to create an advanced security group. This parameter takes effect only if `security_group_id` is left empty.

**Note:** You must specify an advanced security group for a cluster that has Terway installed.

-   `true`: creates an advanced security group.
-   `false`: does not create an advanced security group.

Default value: `false`. |
|service\_cidr|String|Yes|172.21.0.0/20|The CIDR block of Services. This CIDR block cannot overlap with the CIDR block of pods or the CIDR block of the VPC in which the cluster is deployed. If the VPC is automatically created by the system, the CIDR block of Services is set to 172.19.0.0/20 by default. |
|tags|Array of [tag](/intl.en-US/API Reference/Commonly used parameters.md)|No|\[\{"key": "env", "value": "prod"\}\]|The labels that you want to add to the cluster. A label consists of the following parts:

-   `key`: the key of the label.
-   `value`: the value of the label. |
|timezone|String|No|Asia/Shanghai|The time zone of the cluster. |
|taints|Array of [taint](/intl.en-US/API Reference/Commonly used parameters.md)|No|\[\{"key": "env", "value": "private", "effect": "NoSchedule"\}\]|The taints that you want to add to nodes. Taints are added to nodes to prevent pods from being scheduled to inappropriate nodes. However, toleration rules allow pods to be scheduled to nodes with matching taints. For more information, see [taint-and-toleration](https://kubernetes.io/zh/docs/concepts/scheduling-eviction/taint-and-toleration/). |
|cluster\_domain|String|No|cluster.local|The domain name of the cluster.

The domain name consists of one or more parts that are separated by periods \(.\).Each part cannot exceed 63 characters in length, and can contain lowercase letters, digits, and hyphens \(-\). Each part must start and end with a lowercase letter or digit. |
|custom\_san|String|No|cs.aliyun.com|Specifies custom subject alternative names \(SANs\) for the API server certificate to accept requests from specified IP addresses or domain names. Multiple IP addresses and domain names are separated with commas \(,\). |
|service\_account\_issuer|String|No|kubernetes.default.svc|A service account is used to provide an identity for pods when they communicate with the `API server`. `service-account-issuer` is the issuer of the `service account token`, which corresponds to the `iss` field in the `token payload`.

For more information about `service accounts`, see [t1881266.dita\#task\_2460323](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Infrastructure security/Enable service account token volume projection.md). |
|api\_audiences|String|No|kubernetes.default.svc|A service account is used to provide an identity for pods when they communicate with the `API server`. `api-audiences` are valid identifiers of `tokens`. Audiences are used to validate `tokens` at the `API server` side. Separate multiple `audiences` with commas \(,\).

For more information about `service accounts`, see [t1881266.dita\#task\_2460323](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Infrastructure security/Enable service account token volume projection.md). |
|node\_name\_mode|String|No|aliyun.com00055test|The custom node name.

A node name consists of a prefix, an IP substring, and a suffix.

-   The prefix and suffix can contain one or more parts that are separated with periods \(.\).Each part can contain lowercase letters, digits, and hyphens \(-\), and must start and end with a digit or lowercase letter.
-   The IP substring length specifies the number of digits to be truncated from the end of the node IP address. The IP substring length ranges from 5 to 12.

For example, if the node IP address is 192.168.0.55, the prefix is aliyun.com, the IP substring length is 5, and the suffix is test, the node name is aliyun.com00055test. |
|rds\_instances|Array of String|No|rm-2zev748xi27xc\*\*\*\*|The names of ApsaraDB RDS instances. |
|image\_id|String|No|m-bp16z7xko3vvv8gt\*\*\*\*|Specifies a custom image for nodes. By default, the system image is used. You can select a custom image to replace the default image. For more information, see [t1849273.dita\#task\_2362493](/intl.en-US/Best Practices/Migrate a self-built Kubernetes cluster to Container Service for Kubernetes/Use a custom image to create an ACK cluster.md). |
|pod\_vswitch\_ids|Array of String|Yes|vsw-2ze97jwri7cei0mpw\*\*\*\*|Specifies the vSwitches that are allocated to pods. You must set this parameter if the cluster has Terway installed because each pod in the cluster uses a separate IP address. |
|instances|Array of String|No|i-2ze4zxnm36vq00xn\*\*\*\*|The names of instances. |
|format\_disk|Boolean|No|false|Specifies whether to mount a data disk to nodes that are created on existing Elastic Compute Service \(ECS\) instances. Valid values:

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
|timeout\_mins|Long|No|60|Specifies the timeout period of cluster creation. Unit: minutes.

Default value: 60. |
|vpcid|String|Yes|vpc-2zeik9h3ahvv2zz95\*\*\*\*|The ID of the VPC where you want to deploy the cluster. |
|worker\_auto\_renew|Boolean|No|true|Specifies whether to enable auto-renewal for worker nodes. This parameter takes effect only if `worker_instance_charge_type` is set to `PrePaid`. Valid values:

-   `true`: enables auto-renewal.
-   `false`: disables auto-renewal.

Default value: `true`. |
|worker\_auto\_renew\_period|Long|No|1|The cycle of auto-renewal. This parameter takes effect and is required only if the subscription billing method is selected for worker nodes.

Valid values: 1, 2, 3, 6, and 12. |
|worker\_data\_disks|Array of [data\_disk](/intl.en-US/API Reference/Commonly used parameters.md)|No|\[\{"category": "cloud\_ssd", "size": "40", "auto\_snapshot\_policy\_id": "sp-bp14j6w7ss6ozzbp\*\*"\}\]|The configurations of the data disks that are mounted to worker nodes. Each configuration includes disk type and disk size.|
|worker\_data\_disk\_category|String|No| |The category of the data disk. Valid values:-   `cloud_efficiency`: ultra disk.
-   `cloud_ssd`: standard SSD.
-   `cloud`: basic disk.

Default value: `cloud_efficiency`. **Note:** This parameter is obsolete and replaced by the category field in the worker\_data\_disks parameter. |
|worker\_data\_disk\_size|Long|No| |The size of the data disk. Unit: GiB. **Note:** This parameter is obsolete and replaced by the size field in the worker\_data\_disks parameter. |
|worker\_instance\_charge\_type|String|No|PrePaid|The billing method of worker nodes. Valid values:

-   `PrePaid`: subscription
-   `PostPaid`: pay-as-you-go.

Default value: PostPaid. |
|worker\_period|Long|No|1|The subscription duration of worker nodes. This parameter takes effect and is required only if `worker_instance_charge_type` is set to `PrePaid`.

Valid values: 1, 2, 3, 6, 12, 24, 36, 48, and 60.

Default value: 1 |
|worker\_period\_unit|String|No|Month|The billing cycle of worker nodes. This parameter is required if worker\_instance\_charge\_type is set to `PrePaid`.

Set the value to `Month`. Worker nodes can be billed only on a monthly basis. |
|worker\_instance\_types|Array of String|Yes|\["ecs.n4.xlarge"\]|The instance types of worker nodes. You must specify at least one instance type. For more information, see [Instance families](/intl.en-US/Instance/Instance families.md).

**Note:** The instance types are listed in descending order of priority. If worker nodes fail to be created based on the instance type of the highest priority, the system automatically attempts to create worker nodes by using the instance type of the next highest priority. |
|cpu\_policy|String|No|none|The CPU policy. The following policies are supported if the cluster version is 1.12.6 or later.

-   `static`: This policy allows pods with specific resource characteristics on the node to be granted with enhanced CPU affinity and exclusivity.
-   `none`: This policy indicates that the default CPU affinity is used.

Default value: `none`. |
|runtime|[runtime](/intl.en-US/API Reference/Commonly used parameters.md)|No|\{"name": "docker", "version": "19.03.5"\}|The container runtime of the cluster. The following runtimes are supported: `containerd`, `Docker`, and `Sandboxed-Container`. The default runtime is `Docker`. You must specify the name and version of the container runtime. -   `name`: the name of the container runtime.
-   `version`: the version of the container runtime.

For more information about how to select a proper container runtime, see [t1879872.dita\#task\_2455499](/intl.en-US/User Guide for Kubernetes Clusters/Sandboxed-Container management/Comparison of Docker, containerd, and Sandboxed-Container.md). |
|platform|String|No|CentOS|The release version of the operating system. Valid values:

-   `CentOS`
-   `AliyunLinux`
-   `QbootAliyunLinux`
-   `Qboot`
-   `Windows`
-   `WindowsCore`

Default value: `CentOS`. |
|user\_data|String|No|IyEvdXNyL2Jpbi9iYXNoCmVjaG8gIkhlbGxvIEFD\*\*\*\*|The user-defined data. For more information, see [t9660.dita\#concept\_fgf\_tjn\_xdb](/intl.en-US/Instance/Manage instance configurations/Manage instance user data/Overview of ECS instance user data.md). |
|os\_type|String|No|Linux|The type of operating system. Valid values:

-   `Windows`
-   `Linux`

Default value: `Linux`. |
|soc\_enabled|Boolean|No|false|Valid values:

-   `true`: enables reinforcement based on classified protection.
-   `false`: disables reinforcement based on classified protection.

Default value: `false`. |
|cis\_enabled|Boolean|No|false|Specifies whether to enable Center for Internet Security \(CIS\) reinforcement. For more information, see [CIS reinforcement](~~223744~~).

Valid values:

-   `true`: enables CIS reinforcement.
-   `false`: disables CIS reinforcement.

Default value: `false`. |
|node\_cidr\_mask|String|No|25|The maximum number of IP addresses that can be assigned to nodes. This number is determined by the specified pod CIDR block. This parameter takes effect only if the cluster uses the Flannel plug-in.

Default value: 25. |
|kubernetes\_version|String|No|1.16.9-aliyun.1|The version of the cluster. The cluster versions provided by ACK are consistent with the open source versions. We recommend that you select the latest version. If you do not specify this parameter, the latest version is used. You can create clusters of the latest two versions in the ACK console. You can create ACK clusters of other versions by calling API operations. For more information about the Kubernetes versions supported by ACK, see [Overview of Kubernetes versions supported by ACK](/intl.en-US/Release notes/Kubernetes release notes/Overview of Kubernetes versions supported by ACK.md). |
|controlplane\_log\_ttl|String|No|30s|The interval at which the log of control plane components is collected. |
|controlplane\_log\_project|String|No|k8s-log-xxx|The Log Service project that is used to store the log of control plane components. You can use an existing project or create one. If you choose to create a Log Service project, the created project is named `k8s-log-{ClusterID}`. |
|controlplane\_log\_components|Array of String|No|\["apiserver","kcm","scheduler"\]|The list of control plane components of which you want to collect the log.

By default, the log of kube-apiserver, kube-controller-manager, and kube-scheduler is collected. |
|deletion\_protection|Boolean|No|true|Specifies whether to enable deletion protection for the cluster. After deletion protection is enabled, the cluster cannot be deleted in the ACK console or by calling API operations. Valid values:

-   `true`: enables deletion protection for the cluster.
-   `false`: disables deletion protection for the cluster.

Default value: `false`. |

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
<Common request headers>
{
    "name":"managed Kubernetes cluster",                      // The name of the cluster. #required
    "cluster_type":"ManagedKubernetes",     // The type of cluster. #required
    "disable_rollback":true,                // Specifies whether a rollback is performed when the cluster fails to be created. 
    "timeout_mins":60,                      // The timeout period of cluster creation. 
    "kubernetes_version":"1.18.8-aliyun.1", // The Kubernetes version of the cluster. Only the latest two versions are available. 
    "region_id":"cn-zhangjiakou",  // The ID of the region where the cluster is deployed. #required 
    "snat_entry":true,             // Specifies whether SNAT rules are configured for the VPC where the cluster is deployed to enable Internet access for the cluster. 
    "cloud_monitor_flags":true,    // Specifies whether the CloudMonitor agent is installed on ECS instances. 
    "endpoint_public_access":true, // Specifies whether Internet access is enabled for the cluster.  
    "controlplane_log_ttl" : "30s",
    "controlplane_log_project" : "k8s-log-xxx",
    "controlplane_log_components" : ["apiserver","kcm","scheduler"],
    "deletion_protection":true,    // Specifies whether deletion protection is enabled for the cluster. 
    "node_cidr_mask":"26",         // The maximum number of IP addresses that can be assigned to each node. This number is determined by the subnet mask of the specified CIDR block. 
    "proxy_mode":"ipvs",           // The kube-proxy mode. Valid values: iptables and ipvs. 
    "tags":[                       // The labels that are added to the cluster. The labels are applied to the ACK cluster, ECS instances, and nodes in the cluster. 
        {
            "key":"tag-k",
            "value":"tag-v"
        }
    ],
    "timezone":"Asia/Shanghai",   // The time zone of the cluster.
    "addons":[                    // The components that are installed for the cluster.
        {
            "name":"flannel"      // To install the Terway plug-in for the cluster, replace the value with {"name":"terway-eni"}. 
        },
        {
            "name":"csi-plugin"
        },
        {
            "name":"csi-provisioner"
        },
        {
            "name":"logtail-ds",
            "config":"{\"IngressDashboardEnabled\":\"true\"}"
        },
        {
            "name":"ack-node-problem-detector",
            "config":"{\"sls_project_name\":\"\"}"
        },
        {
            "name":"nginx-ingress-controller",                      // The name of the component.
            "config":"{\"IngressSlbNetworkType\":\"internet\"}",    // The configuration of the component.
            "disabled": true                                        // Specifies whether automatic installation is disabled. 
        },
        {
            "name":"arms-prometheus"
        }
    ],
    "cluster_spec":"ack.pro.small",       // The type of managed Kubernetes cluster. ack.pro.small indicates a professional managed Kubernetes cluster. ack.standed indicates a standard managed Kubernetes cluster. 
    "encryption_provider_key":"8734596c-c0d6-4a63-a76e-fe72c7b0****", // The ID of the key that is managed by KMS. The key is used to encrypt Kubernetes Secrets. 
    "os_type":"Linux",        // The type of operating system. Valid values: Linux and Windows. 
    "platform":"AliyunLinux", // The release version of the operating system. Valid values: CentOS, AliyunLinux, Windows, and WindowsCore. 
    "user_data":"IyEvdXNyL2Jpbi9iYXNoCmVjaG8gIkhlbGxvIEFDSyEi", // The user-defined data. 
    "runtime":{             // The container runtime.
        "name":"docker",    // The name of the container runtime.
        "version":"19.03.5" // The version of the container runtime.
    },
    "worker_instance_types":[  // The instance types of worker nodes. #required
        "ecs.t6-c1m4.large"
    ],
    "num_of_nodes":3,                              // The number of worker nodes. #required
    "worker_system_disk_category":"cloud_essd",    // The type of system disk that is specified for worker nodes. #required
    "worker_system_disk_size":120,                 // The size of the system disk that is specified for worker nodes. #required
    "worker_data_disks":[                          // The configurations of the data disks that are mounted to worker nodes.
        {
            "category":"cloud_efficiency",        // The type of data disk. 
            "size":"40",                          // The size of the data disk. Valid values: 40 to 32768. 
            "encrypted":"true",                   // Specifies whether disk encryption is enabled. 
            "auto_snapshot_policy_id":"sp-8vbajx6y2hk21hco****", // The ID of the policy that is used to back up the data disk. 
        }
    ],
    "worker_instance_charge_type":"PrePaid",      // The billing method of worker nodes. Valid values: PostPaid and PrePaid. 
    "worker_period_unit":"Month",                 // The cycle of auto-renewal. Unit: Month. 
    "worker_period":1,                            // The duration of auto-renewal. Default value: 1. 
    "worker_auto_renew":true,                     // Specifies whether auto-renewal is enabled for worker nodes. 
    "worker_auto_renew_period":1,                 // The duration of auto-renewal that takes effect after the worker nodes expire. 
    "vpcid":"vpc-8vbh3b9a2f38urhls****",          // The ID of the VPC where the cluster is deployed.  #required
    "container_cidr":"172.20.0.0/16",             // The CIDR block of pods. #required. This is not required if the network plug-in is Terway. 
    "service_cidr":"172.21.0.0/20",               // The CIDR block of Services.  #required
    "vswitch_ids":[                               // The IDs of vSwitches that are specified for the cluster.  #required
        "vsw-8vbmoffowsztjaawj****"
    ],
    "login_password":"Hello1234",                 // The password that is used to log on to nodes in the cluster as the root user. You must set the login_password or key_pair parameter.  #required
    "key_pair": "key_pair": "sin-name",                       // The key pair that is used to log on to nodes in the cluster as the root user. You must set the login_password or key_pair parameter.  #required
    "cpu_policy":"none",                 // The CPU policy that is specified for nodes in the cluster. Valid values: static and none. 
    "taints":[                           // The taints that are added to nodes. 
        {
            "key":"1",                   // The key of the taint. 
            "value":"1",                 // The value of the taint. 
            "effect":"NoSchedule"        // The pod scheduling policy. 
        }
    ],
    "cluster_domain":"cluster.local",    // The domain name of the cluster. Default value: cluster.local. 
    "custom_san":"cs.aliyuncs.com",      // The custom SANs for the API server certificate. 
    "service_account_issuer":"kubernetes.default.svc", // Service account token volume projection. service_account_issuer is the issuer of the service account token, which corresponds to the iss field in the token payload. 
    "api_audiences":"kubernetes.default.svc",          // Service account token volume projection. api-audiences are valid identifiers of tokens. Audiences are used to validate tokens at the API server side. 
    "node_name_mode":"customized,aliyun.com,5,k8s",    // The user-specified names for nodes. 
    "security_group_id":"sg-8vb7grbyvlb10j0i****",     // The existing security group that is used for the cluster. You must set the security_group_id or is_enterprise_security_group parameter. 
    "is_enterprise_security_group":true,               // Specifies whether an advanced security group is automatically created. You must set the security_group_id or is_enterprise_security_group parameter. 
    "rds_instances": ["rm-xx","rm-xx"],                // The ApsaraDB RDS whitelists. 
    "image_id":"CentOS-xxx",                           // The custom image for nodes. 
    "pod_vswitch_ids":[                                // The vSwitches that are allocated to pods. You must specify vSwitches for pods if the cluster has Terway installed because each pod uses a separate IP address.                      
        "vsw-8vbo5fwyqiw0bbtlq****"
    ],
    "instances": [                // A list of the existing instances that you want to use to create worker nodes. 
        "i-dewgagxdfa****",
        "i-3kjaf9q43l****"
    ],
    "format_disk": false,        // Specifies whether a data disk is mounted to worker nodes. A data disk is formatted after it is mounted to worker nodes. Back up data before you mount a disk. 
    "keep_instance_name": true   // Specifies whether the names of ECS instances are retained. Default value: true. 
}
```

Sample success responses

`XML format`

```
<cluster_id>cb95aa626a47740afbf6aa099b65****</cluster_id>
<task_id>687C5BAA-D103-4993-884B-C35E4314A1E1</task_id>
<request_id>T-5a54309c80282e39ea00002f</request_id>
```

JSON format

```
{
    "cluster_id": "cb95aa626a47740afbf6aa099b65****",
    "request_id": "687C5BAA-D103-4993-884B-C35E4314A1E1",
    "task_id": "T-5a54309c80282e39ea00002f"
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

