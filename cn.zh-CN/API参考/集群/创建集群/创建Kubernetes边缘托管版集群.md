# 创建Kubernetes边缘托管版集群

调用CreateCluster创建一个新的Kubernetes边缘托管版集群实例。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=CS&api=CreateCluster&type=ROA&version=2015-12-15)

## 请求语法

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

## 请求参数

|名称|类型|是否必选|示例值|描述|
|--|--|----|---|--|
|name|String|是|demo-edge-cluster|集群名称。

命名规则：由数字、汉字、英文字符或短划线（-）组成，长度范围1~63个字符，且不能以短划线（-）开头。 |
|cluster\_type|String|是|ManagedKubernetes|集群类型。取值`ManagedKubernetes`创建边缘托管版集群。|
|disable\_rollback|Boolean|否|true|集群创建失败是否回滚。取值：

-   `true`：当集群创建失败时，进行回滚操作。
-   `false`：当集群创建失败时，不进行回滚操作。

默认值：`false`。 |
|timeout\_mins|Long|否|60|集群资源栈创建超时时间，以分钟为单位，默认值60分钟。|
|kubernetes\_version|String|否|1.16.9-aliyun.1|集群版本，与Kubernetes社区基线版本保持一致。建议选择最新版本，若不指定，默认使用最新版本。

目前您可以在ACK控制台创建两种最新版本的集群。您可以通过API创建其他Kubernetes版本集群。关于ACK支持的Kubernetes版本，请参见[Kubernetes版本发布概览](/cn.zh-CN/新功能发布记录/Kubernetes版本发布说明/Kubernetes版本发布概览.md)。 |
|region\_id|String|是|cn-beijing|集群所在地域ID。|
|key\_pair|String|是|demo-key|密钥对名称，和`login_password`二选一。|
|login\_password|String|是|HelloWorld123|SSH登录密码，和`key_pair`二选一。密码规则为8~30个字符，且至少同时包含三项（大小写字母、数字和特殊符号）。|
|num\_of\_nodes|Long|是|1|Worker节点数。范围是\[0,100\]。|
|profile|String|是|Edge|边缘集群标识，默认取值：Edge。|
|snat\_entry|Boolean|否|true|是否为网络配置SNAT：

-   当已有VPC能访问公网环境时，设置为`false`。
-   当已有VPC不能访问公网环境时：
    -   设置为`true`，表示配置SNAT，此时可以访问公网环境。
    -   设置为`false`，表示不配置SNAT，此时不能访问公网环境。

如果您的应用需要访问公网，建议配置为`true`。

默认值：`false`。 |
|vswitch\_ids|Array of String|是|vsw-2ze48rkq464rsdts1\*\*\*\*|交换机ID。List长度范围为\[1,3\]。|
|worker\_system\_disk\_category|String|是|cloud\_efficiency|Worker节点系统盘类型，取值：

-   `cloud_efficiency`：高效云盘。
-   `cloud_ssd`：SSD云盘。

默认值：`cloud_ssd`。 |
|worker\_system\_disk\_size|Long|是|100|Worker节点系统盘大小，单位为GiB。

取值范围：\[40,500\]。

该参数的取值必须大于或者等于max\{40, ImageSize\}。

默认值：`120`。 |
|container\_cidr|String|是|172.20.0.0|Pod网络地址段，不能和VPC网段冲突。当选择系统自动创建VPC时，默认使用172.16.0.0/16网段。

**说明：** 当创建Flannel网络类型的集群时，该字段为必填。 |
|cloud\_monitor\_flags|Boolean|否|true|集群是否安装云监控插件。取值：

-   `true`：安装云监控插件。
-   `false`：不安装云监控插件。

默认值：`false`。 |
|endpoint\_public\_access|Boolean|否|true|是否开启公网API Server。取值：

-   `true`：表示开放公网API Server。
-   `false`：表示不会创建公网的API Server，仅创建私网的API Server。

默认值：`true`。

**说明：** 在边缘集群场景，边缘节点通过公网和云端管控交互；因此，边缘集群需要开启公网访问。 |
|service\_cidr|String|是|172.21.0.0|Service网络地址段，不能和VPC网段及Pod网络网段冲突。当选择系统自动创建VPC时，默认使用172.19.0.0/20网段。 |
|addons|Array of [addon](/cn.zh-CN/API参考/通用数据结构.md)|否|\[\{"name":"flannel","config":""\},\{"name":"logtail-ds-docker","config":""\},\{"name":"alibaba-log-controller","config":"\{"IngressDashboardEnabled":"false"\}"\}\]|Kubernetes集群安装的组件列表。组件的结构包括：

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

**事件中心**：可选，默认开启。事件中心提供对Kubernetes事件的存储、查询、告警等能力。Kubernetes事件中心关联的Logstore在90天内免费。关于免费策略的更多信息，请参见

开启事件中心：\[\{"name":"ack-node-problem-detector","config":"\{\\"sls\_project\_name\\":\\"

your\_sls\_project\_name\\"\}"\}\]。 |
|tags|Array of [tag](/cn.zh-CN/API参考/通用数据结构.md)|否|\[\{"key": "env", "value": "prod"\}\]|给集群打tag标签： -   key：标签名称。
-   value：标签值。 |
|vpcid|String|是|vpc-2zeik9h3ahvv2zz95\*\*\*\*|集群使用的专有网络，创建集群时必须为集群提供。**说明：** `vpc_id`和`vswitch_ids`只能同时为空或者同时都设置对应的值。 |
|worker\_data\_disks|Array of[data\_disk](/cn.zh-CN/API参考/通用数据结构.md)|否| |Worker节点数据盘类型、大小等配置的组合。|
|deletion\_protection|Boolean|否|true|集群删除保护，防止通过控制台或API误删除集群。取值：

-   `true`：启用集群删除保护，将不能通过控制台或API删除集群。
-   `false`：不启用集群删除保护，则能通过控制台或API删除集群。

默认值：`false`。 |
|node\_cidr\_mask|String|否|25|节点IP数量，通过指定网络的CIDR来确定IP的数量，只对于Flannel网络类型集群生效。

默认值：`26`。 |
|worker\_instance\_types|Array of String|是|ecs.n4.large|Worker节点实例规格，至少要指定一个实例规格。更多信息，请参见[实例规格族](/cn.zh-CN/实例/实例规格族.md)。

**说明：** 实例规格优先级随着在数据中的位置增大依次降低。当无法根据优先级较高的实例规格创建出实例时，会自动选择下一优先级的实例规格来创建实例。 |
|worker\_instance\_charge\_type|String|是|PrePaid|Worker节点付费类型，取值：

-   `PrePaid`：包年包月。
-   `PostPaid`：按量付费。

默认值：按量付费。 |
|security\_group\_id|String|否|sg-bp1bdue0qc1g7k\*\*\*\*|使用已有安全组创建集群时需要指定安全组ID，和`is_enterprise_security_group`二选一，集群节点自动加入到此安全组。|
|is\_enterprise\_security\_group|Boolean|否|true|自动创建企业级安全组，当`security_group_id`为空的时生效。

**说明：** 使用普通安全组时，集群内节点与Terway Pod数量之和不能超过2000。所以创建Terway网络类型集群时，建议使用企业安全组。

-   `true`：创建并使用企业级安全组。
-   `false`：不使用企业级安全组。

默认值：`true`。 |
|rds\_instances|rds\_instances|否|rm-2zev748xi27xc\*\*\*\*|RDS实例名称。|

## 响应体语法

```
HTTP/1.1 200
Content-Type:application/json
{
  "cluster_id" : "String",
  "request_id" : "String",
  "task_id" : "String"
}
```

## 响应参数

|名称|类型|示例值|描述|
|--|--|---|--|
|cluster\_id|String|cb95aa626a47740afbf6aa099b650\*\*\*\*|集群ID。 |
|request\_id|String|687C5BAA-D103-4993-884B-C35E4314A1E1|请求ID。 |
|task\_id|String|T-5a54309c80282e39ea00002f|任务ID。 |

## 创建ACK边缘托管版集群示例

请求示例

```
POST /clusters 
<公共请求头>
{
    "name":"ACK边缘托管版",
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
    "profile":"Edge",            // 边缘集群标识。
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

正常返回示例

`XML` 格式

```
<cluster_id>cb95aa626a47740afbf6aa099b650****</cluster_id>
<task_id>T-5a54309c80282e39ea00002f</task_id>
<request_id>687C5BAA-D103-4993-884B-C35E4314A1E1</request_id>
```

`JSON` 格式

```
{
    "cluster_id": "cb95aa626a47740afbf6aa099b650****",
    "task_id": "T-5a54309c80282e39ea00002f",
    "request_id": "687C5BAA-D103-4993-884B-C35E4314A1E1"
}
```

## 错误码

访问[错误中心](https://error-center.aliyun.com/status/product/CS)查看更多错误码。

## 开发者资源

-   [SDK](https://next.api.aliyun.com/api-tools/sdk/CS?version=2015-12-15&)

    阿里云为您提供多种语言的SDK，帮助您快速通过API集成阿里云的产品和服务，推荐您使用SDK调用API，已免除您手动签名验证，详情请参见SDK参考文档链接。

-   [OpenAPI Explorer](https://next.api.aliyun.com/api/CS/2015-12-15/CreateCluster)

    快速检索，可视化调试API，在线命令行工具，同步动态生成可执行的SDK代码示例。

-   [阿里云CLI](https://github.com/aliyun/aliyun-cli)

    阿里云资产管理和配置工具，可通过命令方式同时管理多个阿里云产品和服务，简单快捷，是您上云好帮手。


