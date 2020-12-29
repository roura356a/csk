# 创建Kubernetes边缘托管版集群

调用CreateCluster创建一个新的Kubernetes边缘托管版集群实例。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=CS&api=CreateCluster&type=ROA&version=2015-12-15)

## 请求头

该接口使用公共请求头，无特殊请求头。更多信息，请参见[公共请求参数](公共请求参数t1884803.dita#concept_944293)。

## 请求语法

```
POST /clusters 
```

## 请求参数

|名称|类型|必须|示例值|描述|
|--|--|--|---|--|
|cluster\_type|String|是|ManagedKubernetes|集群类型。取值`ManagedKubernetes`创建边缘托管版集群。|
|key\_pair|String|是|demo-key|密钥对名称，和`login_password`二选一。|
|login\_password|String|是|HelloWorld123|SSH登录密码，和`key_pair`二选一。密码规则为8~30个字符，且至少同时包含三项（大小写字母、数字和特殊符号）。|
|name|String|是|demo-edge-cluster|集群名称。

命名规则：由数字、汉字、英文字符或短划线（-）组成，长度范围1~63个字符，且不能以短划线（-）开头。 |
|num\_of\_nodes|int|是|1|Worker节点数。范围是\[0,100\]。|
|profile|String|是|Edge|边缘集群标识，默认取值：Edge。|
|region\_id|String|是|cn-beijing|集群所在地域ID。|
|snat\_entry|Boolean|是|true|是否为网络配置SNAT：

-   当已有VPC能访问公网环境时，设置为`false`。
-   当已有VPC不能访问公网环境时：
    -   设置为`true`，表示配置SNAT，此时可以访问公网环境。
    -   设置为`false`，表示不配置SNAT，此时不能访问公网环境。

如果您的应用需要访问公网，建议配置为`true`。

默认值：`false`。 |
|vswitch\_ids|list|是|vsw-2ze48rkq464rsdts1\*\*\*\*|交换机ID。List长度范围为 \[1,3\]。|
|worker\_system\_disk\_category|String|是|cloud\_efficiency|Worker节点系统盘类型，取值：

-   `cloud_efficiency`：高效云盘。
-   `cloud_ssd`：SSD云盘。

默认值：`cloud_ssd`。 |
|worker\_system\_disk\_size|int|是|100|Worker节点系统盘大小，单位为GiB。

取值范围：\[20,500\]。 |
|container\_cidr|String|否|172.20.0.0|Pod网络地址段，不能和VPC网段冲突。当选择系统自动创建VPC时，默认使用172.16.0.0/16网段。

**说明：** 当创建Flannel网络类型的集群时，该字段为必填。 |
|cloud\_monitor\_flags|Boolean|否|true|集群是否安装云监控插件。取值：

-   `true`：安装云监控插件。
-   `false`：不安装云监控插件。

默认值：`false`。 |
|disable\_rollback|Boolean|否|true|集群创建失败是否回滚。取值：

-   `true`：当集群创建失败时，进行回滚操作。
-   `false`：当集群创建失败时，不进行回滚操作。

默认值：`false`。 |
|proxy\_mode|String|否|iptables|kube-proxy代理模式，支持`iptables`和`ipvs`两种模式，默认为`ipvs`。 |
|endpoint\_public\_access|Boolean|否|true|是否开启公网API Server。取值：

-   `true`：表示开放公网API Server。
-   `false`：表示不会创建公网的API Server，仅创建私网的API Server。

默认值：`true`。

**说明：** 在边缘集群场景，边缘节点通过公网和云端管控交互；因此，边缘集群需要开启公网访问。 |
|service\_cidr|String|否|172.21.0.0|Service网络地址段，不能和VPC网段及Pod网络网段冲突。当选择系统自动创建VPC时，默认使用172.19.0.0/20网段。 |
|addons|Array|否|\[\{"name":"flannel","config":""\},\{"name":"logtail-ds-docker","config":""\},\{"name":"alibaba-log-controller","config":"\{"IngressDashboardEnabled":"false"\}"\}\]|Kubernetes集群安装的组件列表。组件的结构包括：

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

**事件中心**：可选，默认开启。事件中心提供对Kubernetes事件的存储、查询、告警等能力。Kubernetes事件中心关联的Logstore在90天内免费。关于免费策略的更多信息，请参见[创建并使用Kubernetes事件中心](/intl.zh-CN/应用中心（App）/K8S事件中心/创建并使用Kubernetes事件中心.md)。

开启事件中心：\[\{"name":"ack-node-problem-detector","config":"\{\\"sls\_project\_name\\":\\"

your\_sls\_project\_name\\"\}"\}\]。 |
|tags|list|否|\[\{"key": "env", "value": "prod"\}\]|给集群打tag标签： -   key：标签名称。
-   value：标签值。 |
|timeout\_mins|int|否|60|集群资源栈创建超时时间，以分钟为单位，默认值60分钟。|
|vpcid|String|否|vpc-2zeik9h3ahvv2zz95\*\*\*\*|VPC ID，可空。如果不设置，系统会自动创建VPC，系统创建的VPC网段为192.168.0.0/16。 **说明：** `vpc_id`和`vswitch_ids`只能同时为空或者同时都设置对应的值。 |
|worker\_data\_disk|String|否|true|Worker节点是否挂载数据盘，可选择： -   `true`：表示worker节点挂载数据盘。
-   `false`：表示worker节点不挂载数据盘。 |
|worker\_data\_disk\_category|int|否|cloud\_ssd|数据盘类型。取值：-   `cloud_efficiency`：高效云盘。
-   `cloud_ssd`：SSD云盘。
-   `cloud`：普通云盘。

默认值：`cloud_efficiency`。**说明：** 过期参数，替代参数请参见worker\_data\_disks参数中的category取值。 |
|worker\_data\_disk\_size|String|否|100|数据盘大小，单位为GiB。|

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

访问[错误中心](https://error-center.alibabacloud.com/status/product/CS)查看更多错误码。

