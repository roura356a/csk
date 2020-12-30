# 创建Serverless Kubernetes集群

调用CreateCluster创建一个新的Serverless Kubernetes集群实例。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=CS&api=CreateCluster&type=ROA&version=2015-12-15)

## 请求头

该接口使用公共请求头，无特殊请求头。更多信息，请参见[公共请求参数](公共请求参数t1884803.dita#concept_944293)。

## 请求语法

```
POST /clusters 
```

## 请求参数

|名称|类型|位置|是否必选|示例值|描述|
|--|--|--|----|---|--|
|cluster\_type|String|Body|是|Ask|集群类型。取值`Ask`创建标准Serverless集群。 |
|name|String|Body|是|cluster-demo|集群名称。

命名规则：由数字、汉字、英文字符或短划线（-）组成，长度范围1~63个字符，且不能以短划线（-）开头。 |
|kubernetes\_version|String|Body|否|1.16.9-aliyun.1|集群版本，与Kubernetes社区基线版本保持一致。建议选择最新版本，若不指定，默认使用最新版本。

目前您可以在ACK控制台创建两种最新版本的集群。您可以通过API创建其他Kubernetes版本集群。关于ACK支持的Kubernetes版本，请参见[Kubernetes版本发布概览](/cn.zh-CN/新功能发布记录/Kubernetes版本发布说明/Kubernetes版本发布概览.md)。 |
|private\_zone|Boolean|Body|否|false|是否开启PrivateZone用于服务发现。取值：-   `true`：开启PrivateZone用于服务发现。
-   `false`：不开启PrivateZone用于服务发现。

更多信息，请参见[Serverless集群基于云解析PrivateZone的服务发现](/cn.zh-CN/Serverless Kubernetes集群用户指南/应用管理/Serverless集群基于云解析PrivateZone的服务发现.md)。|
|region\_id|String|Body|是|cn-beijing|集群所在地域ID。|
|endpoint\_public\_access|Boolean|Body|否|true|是否开启公网API Server。取值：

-   `true`：表示开放公网API Server。
-   `false`：表示不会创建公网的API Server，仅创建私网的API Server。

默认值：`true`。 |
|zone\_id|String|Body|是|cn-beiji\*\*\*\*|集群所属地域的可用区ID。 |
|tags|list|Body|否|\[\{"key": "env", "value": "prod"\}\]|给集群打tag标签。包含以下信息： -   `key`：标签名称。
-   `value`：标签值。 |
|deletion\_protection|Boolean|Body|否|true|集群是否开启集群删除保护，防止通过控制台或API误删除集群。取值：

-   `true`：集群开启集群删除保护。
-   `false`：集群不开启集群删除保护。

默认值：`false`。 |
|addons|Array|Body|否|\[\{"name":"logtail-ds","config":"\{"sls\_project\_name":"your\_sls\_project\_name"\}"\}\]|Kubernetes集群安装的组件列表。组件的结构包括：

-   `name`：必填，组件名称。
-   `config`：可选，取值为空时表示无需配置。
-   `disabled`：可选，是否禁止默认安装。

**网络组件**：必选，包含Flannel和Terway两种网络类型，创建集群时二选一：

-   Flannel网络：\[\{"name":"flannel","config":""\}\]。
-   Terway网络：\[\{"name": "terway-eniip","config": ""\}\] 。

**存储组件**：必选，支持`csi`和`flexvolume`两种类型：

-   `csi`：\[\{"name":"csi-plugin","config": ""\},\{"name": "csi-provisioner","config": ""\}\]。
-   `flexvolume`：\[\{"name": "flexvolume","config": ""\}\] 。

**日志组件**：可选。

**说明：** 如果不开启日志服务时，将无法使用集群审计功能。

-   使用已有SLS Project：\[\{"name": "logtail-ds","config": "\{\\"IngressDashboardEnabled\\":\\"true\\",\\"sls\_project\_name\\":\\"your\_sls\_project\_name\\"\}"\}\] 。
-   创建新的SLS Project：\[\{"name": "logtail-ds","config": "\{\\"IngressDashboardEnabled\\":\\"true\\"\}"\}\] 。

**Ingress组件**：可选，ACK专有版集群默认安装Ingress组件nginx-ingress-controller。

-   安装Ingress并且开启公网：\[\{"name":"nginx-ingress-controller","config":"\{\\"IngressSlbNetworkType\\":\\"internet\\"\}"\}\] 。
-   不安装Ingress：\[\{"name": "nginx-ingress-controller","config": "","disabled": true\}\] 。

**事件中心**：可选，默认开启。事件中心提供对Kubernetes事件的存储、查询、告警等能力。Kubernetes事件中心关联的Logstore在90天内免费。关于免费策略的更多信息，请参见[创建并使用Kubernetes事件中心](/cn.zh-CN/应用中心（App）/K8S事件中心/创建并使用Kubernetes事件中心.md)。

开启事件中心：\[\{"name":"ack-node-problem-detector","config":"\{\\"sls\_project\_name\\":\\"

your\_sls\_project\_name\\"\}"\}\]。 |
|nat\_gateway|Boolean|Body|否|true|为专有网络配置SNAT。取值：

-   `true`：将为您创建NAT网关并自动配置SNAT规则。

**说明：** 如果您集群内的节点、应用等需要访问公网，该参数需被设置为`true`。

-   `false`：不为您创建NAT网关及SNAT规则。这种模式下，集群内节点及应用将不能访问公网。

**说明：** 如果创建集群时未开启专有网络配置SNAT，后续业务需要访问公网，可手动开启。更多信息，请参见[如何为已有集群开启SNAT？](/cn.zh-CN/Kubernetes集群用户指南/集群管理/连接集群/如何为已有集群开启SNAT？.md)。

默认值：`true`。 |
|vpc\_id|String|Body|是|vpc-2zeik9h3ahvv2zz95\*\*\*\*|集群使用的专有网络，创建集群时必须为集群提供。**说明：** `vpcid`和`vswitch_ids`只能同时都设置对应的值。 |
|vswitch\_ids|list|Body|是|\["vsw-2ze97jwri7cei0mpw\*\*\*\*"\]|交换机ID。List长度范围为 \[1，3\]。|
|security\_group\_id|String|Body|否|sg-bp1bdue0qc1g7k\*\*\*\*|使用已有安全组创建集群时需要指定安全组ID，和`is_enterprise_security_group`二选一，集群节点自动加入到此安全组。 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|cluster\_id|String|cb95aa626a47740afbf6aa099b650\*\*\*\*|集群ID。 |
|request\_id|String|687C5BAA-D103-4993-884B-C35E4314A1E1|请求ID。 |
|task\_id|String|T-5a54309c80282e39ea00002f|任务ID。 |

## 示例

请求示例

```
POST /clusters 
<公共请求头>
{
    "cluster_type":"Ask",
    "name":"ASK集群",
    "kubernetes_version":"1.18.8-aliyun.1",
    "region_id":"cn-zhangjiakou",
    "endpoint_public_access":true,
    "service_discovery_types":[    //服务发现方式。
        "PrivateZone"
    ],
    "tags":[
        {
            "key":"tag-k",
            "value":"tag-v"
        }
    ],
    "deletion_protection":true,
    "service_cidr":"172.21.0.0/20",
    "timezone":"Asia/Shanghai",
    "addons":[
        {
            "name":"nginx-ingress-controller",
            "config":"{\"IngressSlbNetworkType\":\"internet\"}"
        },
        {
            "name":"metrics-server"
        },
        {
            "name":"logtail-ds"
        },
        {
            "name":"knative"    // 开启Knative， 可不开启。
        }
    ],
    "zone_id":"",   // 可用区ID
    "vpc_id":"vpc-8vbh3b9a2f38urhls****",
    "vswitch_ids":[
        "vsw-8vbmoffowsztjaawj****"
    ],
}
```

创建ASK集群时使用已有VPC。请求示例如下。

```
POST /clusters 
<公共请求头>
{
    "cluster_type":"Ask",
    "name":"test-ask",
    "kubernetes_version":"1.16.9-aliyun.1",
    "region_id":"cn-hangzhou",
    "endpoint_public_access":false,
    "private_zone":false,
    "nat_gateway":false,
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

返回示例

```
{
    "cluster_id": "cb95aa626a47740afbf6aa099b650****",
    "RequestId": "687C5BAA-D103-4993-884B-C35E4314A1E1",
    "task_id": "T-5a54309c80282e39ea00002f",
}
```

## 错误码

访问[错误中心](https://error-center.aliyun.com/status/product/CS)查看更多错误码。

