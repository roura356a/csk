# Create a managed edge Kubernetes cluster

You can call the CreateCluster operation to create a managed edge Kubernetes cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=CreateCluster&type=ROA&version=2015-12-15)

## Request syntax

```
POST /clusters HTTP/1.1
Content-Type:application/json
{
  "name" : "String",
  "cluster_type" : "String",
  "disable_rollback" : Boolean,
  "timeout_mins" : Long,
  "kubernetes_version" : "String",
  "region_id" : "String",
  "key_pair" : "String",
  "login_password" : "String",
  "num_of_nodes" : Long,
  "profile" : "String",
 "logging_type" : "String",
  "snat_entry" : Boolean,
  "vswitch_ids" : [ "String" ],
  "worker_system_disk_category" : "String",
  "worker_system_disk_size" : Long,
  "container_cidr" : "String",
  "cloud_monitor_flags" : Boolean,
  "endpoint_public_access" : Boolean,
  "service_cidr" : "String",
  "addons" : [ {
    "name" : "String",
    "config" : "String",
    "disabled" : Boolean
  } ],
  "tags" : [ {
    "key" : "String",
    "value" : "String"
  } ],
  "vpcid" : "String",
  "worker_data_disks" : [ {
    "category" : "String",
    "size" : Long,
    "encrypted" : "String",
    "auto_snapshot_policy_id" : "String"
  } ],
  "deletion_protection" : Boolean,
  "node_cidr_mask" : "String",
  "worker_instance_types" : [ "String" ],
  "worker_instance_charge_type" : "String",
  "security_group_id" : "String",
  "is_enterprise_security_group" : Boolean,
  "rds_instances" : [ "String" ]
}
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|name|String|Yes|demo-edge-cluster|The name of the cluster.

The name must be 1 to 63 characters in length, and can contain digits, letters, and hyphens \(-\). It cannot start with a hyphen \(-\). |
|cluster\_type|String|Yes|ManagedKubernetes|The type of cluster. Set the value to `ManagedKubernetes` to create a managed edge Kubernetes cluster.|
|disable\_rollback|Boolean|No|true|Specifies whether to perform a rollback when the cluster fails to be created. Valid values:

-   `true`: performs a rollback when the cluster fails to be created.
-   `false`: does not perform a rollback when the cluster fails to be created.

Default value: `false`. |
|timeout\_mins|Long|No|60|The timeout period in minutes during which a resource must be created. Default value: 60.|
|kubernetes\_version|String|No|1.16.9-aliyun.1|The Kubernetes version of the cluster. The Kubernetes versions provided by Container Service for Kubernetes \(ACK\) are consistent with the open source Kubernetes versions. We recommend that you select the latest Kubernetes version. If you do not specify this parameter, the latest Kubernetes version is used.

You can create clusters of the latest two Kubernetes versions in the ACK console. You can create ACK clusters of earlier Kubernetes versions by calling API operations. For more information about the Kubernetes versions supported by ACK, see [Overview of Kubernetes versions supported by ACK](/intl.en-US/Release notes/Kubernetes release notes/Overview of Kubernetes versions supported by ACK.md). |
|region\_id|String|Yes|cn-beijing|The ID of the region where you want to deploy the cluster.|
|key\_pair|String|Yes|demo-key|The name of the key pair. You must set this parameter or the `login_password` parameter.|
|login\_password|String|Yes|HelloWorld123|The password for SSH logon. You must set this parameter or the `key_pair` parameter. The password must be 8 to 30 characters in length, and must contain at least three of the following character types: uppercase letters, lowercase letters, digits, and special characters.|
|num\_of\_nodes|Long|Yes|1|The number of worker nodes that you want to create. Valid values: 0 to 100.|
|profile|String|Yes|Edge|Specifies whether the cluster to be created is an edge Kubernetes cluster. Default value: Edge.|
|logging\_type|String|No|SLS|Enables Log Service for the cluster. This parameter takes effect only for serverless Kubernetes \(ASK\) clusters. Set the value to `SLS`. |
|snat\_entry|Boolean|No|true|Specifies whether to configure Source Network Address Translation \(SNAT\) rules for the virtual private cloud \(VPC\) where the cluster is deployed.

-   If the VPC supports Internet access, set the value to `false`.
-   If the VPC does not support Internet access, valid values are:
    -   `true`: configures SNAT rules. This enables Internet access for the cluster.
    -   `false`: does not configure SNAT rules. In this case, the cluster cannot access the Internet.

If your applications deployed in the cluster need to access the Internet, we recommend that you set the value to `true`.

Default value: `false`. |
|vswitch\_ids|Array of String|Yes|vsw-2ze48rkq464rsdts1\*\*\*\*|The IDs of the vSwitches. You can specify one to three vSwitches.|
|worker\_system\_disk\_category|String|Yes|cloud\_efficiency|The type of system disk that is specified for worker nodes. Valid values:

-   `cloud_efficiency`: ultra disk
-   `cloud_ssd`: standard SSD

Default value: `cloud_ssd`. |
|worker\_system\_disk\_size|Long|Yes|100|The size of the system disk that is specified for worker nodes. Unit: GiB.

Valid values: 40 to 500.

The value of this parameter must be at least 40 and no less than the image size.

Default value: `120`. |
|container\_cidr|String|Yes|172.20.0.0|The CIDR block of pods. This CIDR block cannot overlap with the CIDR block of the VPC where the cluster is deployed. If the VPC is automatically created by the system, the CIDR block of pods is set to 172.16.0.0/16 by default.

**Note:** This parameter is required if the cluster uses the Flannel plug-in. |
|cloud\_monitor\_flags|Boolean|No|true|Specifies whether to install the CloudMonitor agent. Valid values:

-   `true`: installs the CloudMonitor agent.
-   `false`: does not install the CloudMonitor agent.

Default value: `false`. |
|endpoint\_public\_access|Boolean|No|true|Specifies whether to enable Internet access for the API server. Valid values:

-   `true`: enables Internet access for the API server.
-   `false`: disables Internet access for the API server. The API server is accessible only within the internal network.

Default value: `true`.

**Note:** Edge nodes interact with controller components in the cloud over the Internet. Therefore, you must enable Internet access for the API server when you create a managed edge Kubernetes cluster. |
|service\_cidr|String|Yes|172.21.0.0|The CIDR block of Services. This CIDR block cannot overlap with the CIDR block of the VPC in which the cluster is deployed or the CIDR block of pods. If the VPC is automatically created by the system, the CIDR block of Services is set to 172.19.0.0/20 by default. |
|addons|Array of [addon](/intl.en-US/API Reference/Commonly used parameters.md)|No|\[\{"name":"flannel","config":""\},\{"name":"logtail-ds-docker","config":""\},\{"name":"alibaba-log-controller","config":"\{"IngressDashboardEnabled":"false"\}"\}\]|The components that you want to install in the cluster. Parameter description:

-   `name`: the name of the component. This parameter is required.
-   `config`: If this parameter is left empty, it indicates that no configurations are required. This parameter is optional.
-   `disabled`: This parameter specifies whether to disable automatic installation. This parameter is optional.

**Network plug-in**: Supported network plug-ins are Flannel and Terway. Select one of the plug-ins for the cluster. This parameter is reuiqred.

-   Specify the Flannel plug-in in the following format: \[\{"name":"flannel","config":""\}\].
-   Specify the Terway plug-in in the following format: \[\{"name": "terway-eniip","config": ""\}\].

**Volume plug-in**: Supported volume plug-ins are `CSI` and `FlexVolume`. This parameter is required.

-   Specify the `CSI` plug-in in the following format: \[\{"name":"csi-plugin","config": ""\},\{"name": "csi-provisioner","config": ""\}\].
-   Specify the `FlexVolume` plug-in in the following format: \[\{"name": "flexvolume","config": ""\}\].

**Log Service component**: optional.

**Note:** If Log Service is disabled, you cannot use the cluster auditing feature.

-   To use an existing Log Service project, specify the component in the following format: \[\{"name": "logtail-ds","config": "\{\\"IngressDashboardEnabled\\":\\"true\\",\\"sls\_project\_name\\":\\"your\_sls\_project\_name\\"\}"\}\].
-   To create a Log Service project, specify the component in the following format: \[\{"name": "logtail-ds","config": "\{\\"IngressDashboardEnabled\\":\\"true\\"\}"\}\].

**Ingress controller**: optional. By default, the nginx-ingress-controller component is installed for dedicated Kubernetes clusters.

-   To install nginx-ingress-controller and enable Internet access, specify the Ingress controller in the following format: \[\{"name":"nginx-ingress-controller","config":"\{\\"IngressSlbNetworkType\\":\\"internet\\"\}"\}\].
-   To disable the automatic installation of nginx-ingress-controller, specify the component in the following format: \[\{"name": "nginx-ingress-controller","config": "","disabled": true\}\].

**Event center**: optional. By default, the event center feature is enabled. The event center feature allows you to log Kubernetes events, query events, and raise alerts. Logstores that are associated with the Kubernetes event center are free of charge for the first 90 days. For more information, see [Create and use a Kubernetes event center](/intl.en-US/Application/K8s Event Center/Create and use a Kubernetes event center.md).

To enable the Kubernetes event center, specify the ack-node-problem-detector component in the following format: \[\{"name":"ack-node-problem-detector","config":"\{\\"sls\_project\_name\\":\\"

your\_sls\_project\_name\\"\}"\}\]. |
|tags|Array of [tag](/intl.en-US/API Reference/Commonly used parameters.md)|No|\[\{"key": "env", "value": "prod"\}\]|The labels that you want to add to the cluster. -   key: the key of the label.
-   value: the value of the label. |
|vpcid|String|Yes|vpc-2zeik9h3ahvv2zz95\*\*\*\*|The VPC where you want to deploy the cluster. You must specify a VPC when you create the cluster. **Note:** Leave both `vpc_id` and `vswitch_ids` empty or make sure that the vSwitches specified by vswitch\_ids belong to the VPC that is specified by vswitch\_ids. |
|worker\_data\_disks|Array of [data\_disk](/intl.en-US/API Reference/Commonly used parameters.md)|No| |The configurations of the data disks that are mounted to worker nodes. Each configuration includes disk type and disk size.|
|deletion\_protection|Boolean|No|true|Specifies whether to enable deletion protection for the cluster. After deletion protection is enabled, the cluster cannot be deleted in the ACK console or by calling API operations. Valid values:

-   `true`: enables deletion protection for the cluster. This way, the cluster cannot be deleted in the ACK console or by calling API operations.
-   `false`: disables deletion protection for the cluster. This way, the cluster can be deleted in the ACK console or by calling API operations.

Default value: `false`. |
|node\_cidr\_mask|String|No|25|The maximum number of IP addresses that can be assigned to each node. This number is determined by the specified pod CIDR block. This parameter takes effect only if the cluster uses the Flannel plug-in.

Default value: `26`. |
|worker\_instance\_types|Array of String|Yes|ecs.n4.large|The instance types of worker nodes. You must specify at least one instance type. For more information, see [Instance families](/intl.en-US/Instance/Instance families.md).

**Note:** The instance types are listed in descending order of priority. If worker nodes fail to be created based on the instance type of the highest priority, the system attempts to create worker nodes by using the instance type of the next highest priority. |
|worker\_instance\_charge\_type|String|Yes|PrePaid|The billing method of worker nodes. Valid values:

-   `PrePaid`: subscription.
-   `PostPaid`: pay-as-you-go.

Default value: PostPaid. |
|security\_group\_id|String|No|sg-bp1bdue0qc1g7k\*\*\*\*|The ID of the existing security group that is specified for the cluster. You must set this parameter or the `is_enterprise_security_group` parameter. Nodes in the cluster are automatically added to the specified security group.|
|is\_enterprise\_security\_group|Boolean|No|true|Specifies whether to create an advanced security group. This parameter takes effect only if `security_group_id` is left empty.

**Note:** To use a basic security group, make sure that the sum of the number of nodes in the cluster plus the number of pods that use Terway does not exceed 2,000. Therefore, we recommend that you specify an advanced security group for a cluster that has Terway installed.

-   `true`: creates an advanced security group.
-   `false`: does not create an advanced security group.

Default value: `true`. |
|rds\_instances|rds\_instances|No|rm-2zev748xi27xc\*\*\*\*|The names of ApsaraDB RDS instances.|
|**cluster\_spec**|String|No|ack.pro.small|The type of managed Kubernetes cluster. Valid values:-   `ack.pro.small`: professional edge Kubernetes cluster.
-   `ack.standard`: standard edge Kubernetes cluster.

Default valid: `ack.standard`. If you leave this parameter empty, a standard edge Kubernetes cluster is created.

For more information, see [Introduction to professional edge Kubernetes clusters](/intl.en-US/User Guide for Edge Container Service/ACK@Edge Pro edition cluster/Introduction to professional edge Kubernetes clusters.md). |

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
|cluster\_id|String|cb95aa626a47740afbf6aa099b650\*\*\*\*|The ID of the cluster. |
|request\_id|String|687C5BAA-D103-4993-884B-C35E4314A1E1|The ID of the request. |
|task\_id|String|T-5a54309c80282e39ea00002f|The ID of the task. |

## Examples

Sample requests

```
POST /clusters 
<Common request headers>
{
    "name":"managed edge Kubernetes cluster",
    "cluster_type":"ManagedKubernetes",
    "disable_rollback":true,
    "timeout_mins":60,
    "kubernetes_version":"1.14.8-aliyunedge.1",
    "region_id":"cn-zhangjiakou",
    "snat_entry":true,
    "cloud_monitor_flags":true,
    "endpoint_public_access":true,
    "deletion_protection":true,
    "node_cidr_mask":"26",
    "tags":[
        {
            "key":"tag-k",
            "value":"tag-v"
        }
    ],
    "addons":[
        {
            "name":"logtail-ds-docker"
        },
        {
            "name":"alibaba-log-controller",
            "config":"{\"IngressDashboardEnabled\":\"false\"}"
        },
        {
            "name":"flannel"
        },
        {
            "name":"alicloud-monitor-controller"
        }
    ],
    "profile":"Edge",            // Specifies that the cluster to be created is an edge cluster. 
    "logging_type" : "SLS",
    "worker_instance_types":[
        "ecs.hfc6.large"
    ],
    "num_of_nodes":1,
    "worker_system_disk_category":"cloud_ssd",
    "worker_system_disk_size":40,
    "worker_data_disks":[
        {
            "category":"cloud_efficiency",
            "size":"40",
            "encrypted":"false",
            "auto_snapshot_policy_id":"",
        }
    ],
    "worker_instance_charge_type":"PostPaid",
    "vpcid":"vpc-8vb435kr467tnfj42****",
    "container_cidr":"172.20.0.0/16",
    "service_cidr":"172.21.0.0/20",
    "vswitch_ids":[
        "vsw-8vbhdhn461i65p32g****"
    ],
    "login_password":"Hello1234",
    "key_pair": "sin-name",
    "security_group_id":"sg-8vb7grbyvlb10j0i****",
    "is_enterprise_security_group":true,
    "rds_instances": ["rm-xx","rm-xx"]
}
```

Successful response examples

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

