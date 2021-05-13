# Create an ASK cluster

Creates a serverless Kubernetes \(ASK\) cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=CreateCluster&type=ROA&version=2015-12-15)

## Request structure

```
POST /clusters HTTP/1.1
Content-Type:application/json
{

  "cluster_type" : "String",
  "name" : "String",
  "kubernetes_version" : "String",
  "private_zone" : Boolean,
  "region_id" : "String",
  "endpoint_public_access" : Boolean,
  "service_discovery_types" : [ "String" ],
  "zone_id" : "String",
  "tags" : [ {
    "key" : "String",
    "value" : "String"
  } ],
  "deletion_protection" : Boolean,
  "service_cidr" : "String",
  "timezone" : "String",
  "addons" : [ {
    "name" : "String",
    "config" : "String",
    "disabled" : Boolean
  } ],
  "nat_gateway" : Boolean,
  "vpcid" : "String",
  "vswitch_ids" : [ "String" ],
  "security_group_id" : "String"
}
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|cluster\_type|String|Yes|Ask|The type of cluster. Set the value to `Ask` to create an ASK cluster. |
|name|String|Yes|cluster-demo|The name of the cluster.

The name must be 1 to 63 characters in length, and can contain digits, letters, and hyphens \(-\). It cannot start with a hyphen. |
|kubernetes\_version|String|No|1.16.9-aliyun.1|The version of the cluster. The cluster versions provided by Container Service for Kubernetes \(ACK\) are consistent with the open source versions. We recommend that you select the latest version. If you do not specify this parameter, the latest version is used.

You can create clusters of the latest two versions in the ACK console. You can create ACK clusters of earlier versions by calling API operations. For more information about the Kubernetes versions supported by ACK, see [Release notes](/intl.en-US/Release notes/Kubernetes release notes/Release notes.md). |
|private\_zone|Boolean|No|false|Specifies whether to enable Alibaba Cloud DNS PrivateZone for Service discovery. Valid values:-   `true`: enables Alibaba Cloud DNS PrivateZone for Service discovery.
-   `false`: disables Alibaba Cloud DNS PrivateZone for Service discovery.

For more information, see [Use the service discovery feature based on Alibaba Cloud DNS PrivateZone in ASK clusters](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Serverless cluster best practices/Use the service discovery feature based on Alibaba Cloud DNS PrivateZone in ASK clusters.md).|
|region\_id|String|Yes|cn-beijing|The ID of the region where you want to deploy the cluster.|
|endpoint\_public\_access|Boolean|No|true|Specifies whether to enable Internet access for the API server. Valid values:

-   `true`: enables Internet access for the API server.
-   `false`: disables Internet access for the API server. The API server is accessible only within the internal network.

Default value: `true`. |
|service\_discovery\_types|Array of String|No|PrivateZone|Specifies the type of Service discovery in `ASK` clusters.

-   `CoreDNS`: a Kubernetes-native standard Service discovery component. You must provision pods to use the Domain Name System \(DNS\) resolution service. By default, two elastic container instances are used. The specification of each instance is 0.25 cores and 512 MiB of memory.
-   `PrivateZone`: a DNS resolution service provided by Alibaba Cloud. Alibaba Cloud DNS PrivateZone must be activated before you can use it for Service discovery.

By default, this parameter is not specified. |
|zone\_id|String|No|cn-beiji\*\*\*\*|The ID of the zone to which the cluster belongs.

**Note:** If you do not set `vpc_id` or `vswitch_ids`, you must set the `zone_id` parameter. |
|tags|Array of [tag](/intl.en-US/API Reference/Commonly used parameters.md)|No|\[\{"key": "env", "value": "prod"\}\]|The labels of the cluster. A label consists of the following parts: -   `key`: the key of the label.
-   `value`: the value of the label. |
|deletion\_protection|Boolean|No|true|Specifies whether to enable deletion protection for the cluster. After deletion protection is enabled, the cluster cannot be deleted in the ACK console or by calling API operations. Valid values:

-   `true`: enables deletion protection for the cluster.
-   `false`: disables deletion protection for the cluster.

Default value: `false`. |
|service\_cidr|String|Yes|172.21.0.0/20|The CIDR block of Services. Valid values: 10.0.0.0/16-24, 172.16-31.0.0/16-24, and 192.168.0.0/16-24.

The CIDR block of Services cannot overlap with that of the virtual private cloud \(VPC\) where the cluster is deployed. The CIDR block of the VPC is 10.1.0.0/21 in this example. In addition, the CIDR block of Services cannot overlap with the CIDR blocks of existing clusters in the VPC. You cannot modify the Service CIDR block after the cluster is created.

By default, the CIDR block of Services is set to 172.19.0.0/20. |
|timezone|String|No|Asia/Shanghai|The time zone of the cluster. |
|addons|Array of [addon](/intl.en-US/API Reference/Commonly used parameters.md)|No|\[\{"name":"logtail-ds","config":"\{"sls\_project\_name":"your\_sls\_project\_name"\}"\}\]|The add-ons to be installed for the cluster. Parameter description:

-   `name`: the name of the add-on. This parameter is required.
-   `config`: This parameter is optional. If this parameter is left empty, it indicates that no configurations are required.
-   `disabled`: specifies whether to disable automatic installation. This parameter is optional.

**Network plug-in**: Required. Supported network plug-ins are Flannel and Terway. Select one of the plug-ins for the cluster.

-   Specify the Flannel plug-in in the following format: \[\{"name":"flannel","config":""\}\].
-   Specify the Terway plug-in in the following format: \[\{"name": "terway-eniip","config": ""\}\].

**Volume plug-in**: Required. Supported volume plug-ins are `CSI` and `FlexVolume`.

-   Specify the `CSI` plug-in in the following format: \[\{"name":"csi-plugin","config": ""\},\{"name": "csi-provisioner","config": ""\}\].
-   Specify the `FlexVolume plug-in` in the following format: \[\{"name": "flexvolume","config": ""\}\].

**Log Service component**: Optional.

**Note:** If Log Service is disabled, you cannot use the cluster auditing feature.

-   To use an existing Log Service project, specify the component in the following format: \[\{"name": "logtail-ds","config": "\{\\"IngressDashboardEnabled\\":\\"true\\",\\"sls\_project\_name\\":\\"your\_sls\_project\_name\\"\}"\}\].
-   To create a Log Service project, specify the component in the following format: \[\{"name": "logtail-ds","config": "\{\\"IngressDashboardEnabled\\":\\"true\\"\}"\}\].

**Ingress controller**: Optional. By default, the nginx-ingress-controller component is installed for dedicated Kubernetes clusters.

-   To install nginx-ingress-controller and enable Internet access, specify the component in the following format: \[\{"name":"nginx-ingress-controller","config":"\{\\"IngressSlbNetworkType\\":\\"internet\\"\}"\}\].
-   If you do not want to install nginx-ingress-controller, specify the component in the following format: \[\{"name": "nginx-ingress-controller","config": "","disabled": true\}\].

**Event center**: Optional. By default, the event center feature is enabled. The event center feature allows you to log Kubernetes events, query events, and raise alerts. Logstores that are associated with the Kubernetes event center are free of charge for the first 90 days. For more information, see [Create and use a Kubernetes event center](/intl.en-US/Application/K8s Event Center/Create and use a Kubernetes event center.md).

Enable the ack-node-problem-detector component in the following format: \[\{"name":"ack-node-problem-detector","config":"\{\\"sls\_project\_name\\":\\"

your\_sls\_project\_name\\"\}"\}\]. |
|nat\_gateway|Boolean|No|true|Specifies whether to create a Network Address Translation \(NAT\) gateway and configure Source Network Address Translation \(SNAT\) rules when you create the ASK cluster. Valid values:

-   `true`: automatically creates a NAT gateway and configures SNAT rules. This enables Internet access for the VPC where the cluster is deployed.
-   `false`: does not create a NAT gateway or configure SNAT rules. In this case, clusters in the VPC cannot access the Internet.

Default value: `false`. |
|vpcid|String|Yes|vpc-2zeik9h3ahvv2zz95\*\*\*\*|The VPC where the cluster is deployed. You must specify a VPC when you create the cluster. **Note:** The vSwitches specified by `vswitch_ids` must belong to the VPC that is specified by `vpcid`. |
|vswitch\_ids|Array of String|Yes|\["vsw-2ze97jwri7cei0mpw\*\*\*\*"\]|The IDs of vSwitches. You can specify up to three vSwitches.|
|security\_group\_id|String|No|sg-bp1bdue0qc1g7k\*\*\*\*|The ID of the existing security group that is specified for the cluster. You must set this parameter or the `is_enterprise_security_group` parameter. Nodes in the cluster are automatically added to the specified security group. |

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
|cluster\_id|String|cb95aa626a47740afbf6aa099b650\*\*\*\*|The ID of the cluster. |
|request\_id|String|687C5BAA-D103-4993-884B-C35E4314A1E1|The ID of the request. |
|task\_id|String|T-5a54309c80282e39ea00002f|The ID of the task. |

## Example 1: Create an ASK cluster in an automatically created VPC

Sample requests

```
POST /clusters HTTP/1.1
<Common request headers>
{
    "cluster_type":"Ask",
    "name":"test-ask",
    "region_id":"cn-hangzhou",
    "endpoint_public_access":false,
    "private_zone":false,
    "nat_gateway":true,
    "tags":[
        {
            "key":"k-aa",
            "value":"v-aa"
        }
    ],
    "deletion_protection":false,
    "addons":[
        {
            "name":"logtail-ds"
        }
    ],
    "zone_id":"cn-hangzhou-i"
}
```

Sample responses

```
{
    "cluster_id": "cb95aa626a47740afbf6aa099b650****",
    "RequestId": "687C5BAA-D103-4993-884B-C35E4314A1E1",
    "task_id": "T-5a54309c80282e39ea00002f",
}
```

## Example 2: Create an ASK cluster in an existing VPC

Sample requests

```
POST /clusters HTTP/1.1
<Common request headers>
{
    "cluster_type":"Ask",
    "name":"ask-cluster",
    "region_id":"cn-shenzhen",
    "endpoint_public_access":true,
    "private_zone":true,
    "tags":[
        {
            "key":"tier",
            "value":"frontend"
        }
    ],
    "deletion_protection":true,
    "addons":[
        {
            "name":"logtail-ds"
        }
    ],
    "vpc_id":"vpc-wz984yvbd6lck22z3****",
    "vswitch_ids":[
        "vsw-wz9uwxhawmtzg7u9h****"
    ],
    "security_group_id":"sg-wz9b86l4s7nthi1k****"
}
```

Sample responses

```
{
    "cluster_id": "cb95aa626a47740afbf6aa099b650****",
    "RequestId": "687C5BAA-D103-4993-884B-C35E4314A1E1",
    "task_id": "T-5a54309c80282e39ea00002f",
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

