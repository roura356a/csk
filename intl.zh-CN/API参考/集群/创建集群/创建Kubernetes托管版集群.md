# 创建Kubernetes托管版集群

调用CreateCluster创建一个Kubernetes托管版集群实例，并创建指定数量的节点。

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
|cluster\_type|String|Body|是|ManagedKubernetes|集群类型。取值`ManagedKubernetes`创建标准托管版集群。|
|key\_pair|String|Body|否|secrity-key|密钥对名称，和`login_password`二选一。|
|login\_password|String|Body|否|Hello@1234|SSH登录密码，和`key_pair`二选一。密码规则为8~30个字符，且至少同时包含三项（大小写字母、数字和特殊符号）。|
|name|String|Body|是|cluster-demo|集群名称。

命名规则：由数字、汉字、英文字符或短划线（-）组成，长度范围1~63个字符，且不能以短划线（-）开头。 |
|num\_of\_nodes|int|Body|是|3|Worker节点数。范围是\[0,100\]。|
|region\_id|String|Body|是|cn-beijing|集群所在地域ID。|
|snat\_entry|Boolean|Body|否|true|是否为网络配置SNAT：

-   当已有VPC能访问公网环境时，设置为`false`。
-   当已有VPC不能访问公网环境时：
    -   设置为`true`，表示配置SNAT，此时可以访问公网环境。
    -   设置为`false`，表示不配置SNAT，此时不能访问公网环境。

如果您的应用需要访问公网，建议配置为true。

默认值：`false`。 |
|vswitch\_ids|list|Body|是|\["vsw-2ze48rkq464rsdts1\*\*\*\*"\]|交换机ID。List长度范围为 \[1,3\]。|
|worker\_system\_disk\_category|String|Body|否|cloud\_efficiency|Worker节点系统盘类型，取值：

-   `cloud_efficiency`：高效云盘。
-   `cloud_ssd`：SSD云盘。

默认值：`cloud_ssd`。 |
|worker\_system\_disk\_size|int|Body|否|120|Worker节点系统盘大小，单位为GiB。

取值范围：\[20,500\]。

该参数的取值必须大于或者等于max\{20, ImageSize\}。

默认值：max\{40, 参数ImageId对应的镜像大小\}。 |
|addons|list|Body|否|\[\{"name": "terway-eniip","config": ""\}, \{"name": "logtail-ds","config": "\{\\"IngressDashboardEnabled\\":\\"true\\",\\"sls\_project\_name\\":\\"your\_sls\_project\_name\\"\}"\}, \{"name":"nginx-ingress-controller","config":"\{\\"IngressSlbNetworkType\\":\\"internet\\"\}"\}\]|Kubernetes集群安装的组件列表。组件的结构包括：

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
|container\_cidr|String|Body|否|172.20.0.0/16|Pod网络地址段，不能和VPC网段冲突。当选择系统自动创建VPC时，默认使用172.16.0.0/16网段。

**说明：** 当创建Flannel网络类型的集群时，该字段为必填。 |
|cloud\_monitor\_flags|Boolean|Body|否|true|集群是否安装云监控插件。取值：

-   `true`：安装云监控插件。
-   `false`：不安装云监控插件。

默认值：`false`。 |
|disable\_rollback|Boolean|Body|否|true|集群创建失败是否回滚。取值：

-   `true`：当集群创建失败时，进行回滚操作。
-   `false`：当集群创建失败时，不进行回滚操作。

默认值：`false`。 |
|public\_slb|Boolean|Body|否|true|是否开启公网API Server。取值： -   true：开放公网API Server。
-   false：不会创建公网的API Server，仅创建私网的API Server。

默认值：`true`。

**说明：** 过期参数，替代参数请参见endpoint\_public\_access。 |
|proxy\_mode|String|Body|否|ipvs|kube-proxy代理模式，支持`iptables`和`ipvs`两种模式，默认为`ipvs`。 |
|endpoint\_public\_access|Boolean|Body|否|true|是否开启公网API Server。取值：

-   `true`：表示开放公网API Server。
-   `false`：表示不会创建公网的API Server，仅创建私网的API Server。

默认值：`true`。 |
|security\_group\_id|String|Body|否|sg-bp1bdue0qc1g7k1e\*\*|使用已有安全组创建集群时需要指定安全组ID，和`is_enterprise_security_group`二选一，集群节点自动加入到此安全组。 |
|is\_enterprise\_security\_group|Boolean|Body|否|true|自动创建企业级安全组，当`security_group_id`为空的时生效。

**说明：** Terway网络类型集群，必须指定为企业安全组。

-   `true`：创建企业级安全组。
-   `false`：不创建企业级安全组。

默认值：`false`。 |
|service\_cidr|String|Body|否|172.21.0.0/20|Service网络地址段，不能和VPC网段及Pod网络网段冲突。当选择系统自动创建VPC时，默认使用172.19.0.0/20网段。 |
|tags|list|Body|否|\[\{"key": "env", "value": "prod"\}\]|给集群打tag标签。包含以下信息：

-   `key`：标签名称。
-   `value`：标签值。 |
|taints|list|Body|否|\[\{"key": "env", "value": "private", "effect": "NoSchedule"\}\]|节点污点信息。污点和容忍度（Toleration）相互配合，可以用来避免Pod被分配到不合适的节点上。更多信息，请参见[taint-and-toleration](https://kubernetes.io/zh/docs/concepts/scheduling-eviction/taint-and-toleration/)。 |
|timeout\_mins|int|Body|否|60|集群创建超时时间，单位分钟。

默认值：60。 |
|vpcid|String|Body|是|vpc-2zeik9h3ahvv2zz95\*\*\*\*|集群使用的VPC实例ID。 |
|worker\_auto\_renew|Boolean|Body|否|true|Worker节点是否开启自动续费，当`worker_instance_charge_type`取值为`PrePaid`时才生效，可选值为：

-   `true`：自动续费。
-   `false`：不自动续费。

默认值：`true`。 |
|worker\_auto\_renew\_period|int|Body|否|1|Worker节点自动续费周期，当选择包年包月付费类型时才生效，且为必选值。

取值范围：\{1, 2, 3, 6, 12\}。 |
|worker\_data\_disks|list|Body|否|\[\{"category": "cloud\_ssd", "size": "40", "auto\_snapshot\_policy\_id": "sp-bp14j6w7ss6ozzbp\*\*"\}\]|Worker数据盘类型、大小等配置的组合。|
|worker\_data\_disk\_category|String|Body|否| |数据盘类型。取值：-   `cloud_efficiency`：高效云盘。
-   `cloud_ssd`：SSD云盘。
-   `cloud`：普通云盘。

默认值：`cloud_efficiency`。**说明：** 过期参数，替代参数请参见worker\_data\_disks参数中的category取值。 |
|worker\_data\_disk\_size|int|Body|否| |数据盘大小，单位为GiB。**说明：** 过期参数，替代参数请参见worker\_data\_disks参数中的size取值。 |
|worker\_instance\_charge\_type|String|Body|否|PrePaid|Worker节点付费类型，取值：

-   `PrePaid`：包年包月。
-   `PostPaid`：按量付费。

默认值：按量付费。 |
|worker\_period|int|Body|否|1|Worker节点包年包月时长，当`worker_instance_charge_type`取值为`PrePaid`时才生效且为必选值。

取值范围：\{1, 2, 3, 6, 12, 24, 36, 48, 60\}。

默认值：1。 |
|worker\_period\_unit|String|Body|否|Month|Wroker节点付费周期，当付费类型为`PrePaid`的时候需要指定周期。

取值：`Month`，当前只支持以月为周期。 |
|worker\_instance\_types|list|Body|是|\["ecs.n4.xlarge"\]|Worker节点实例规格，至少要指定一个实例规格。更多详细信息，请参见[实例规格族](/intl.zh-CN/实例/实例规格族.md)。

**说明：** 实例规格优先级随着在数据中的位置增大依次降低。当无法根据优先级较高的实例规格创建出实例时，会自动选择下一优先级的实例规格来创建实例。 |
|cpu\_policy|String|Body|否|none|节点CPU管理策略。当集群版本在1.12.6及以上时支持以下两种策略：

-   `static`：允许为节点上具有某些资源特征Pod增强其CPU亲和性和独占性。
-   `none`：表示启用现有的默认CPU亲和性方案。

默认值：`none`。 |
|runtime|json|Body|否|\{"name": "docker", "version": "19.03.5"\}|容器运行时，例如docker、Sandboxed-Container.runv等，通常为docker。runtime包括下面2个信息： -   `name`：容器运行时名称。
-   `version`：容器运行时版本。 |
|platform|String|Body|否|CentOS|操作系统发行版。取值：

-   `CentOS`
-   `AliyunLinux`
-   `QbootAliyunLinux`
-   `Qboot`
-   `Windows`
-   `WindowsCore`

默认值：`CentOS`。 |
|os\_type|String|Body|否|Linux|操作系统平台类型。取值：

-   `Windows`
-   `Linux`

默认值：`Linux`。 |
|node\_cidr\_mask|String|Body|否|25|节点IP数量，通过指定网络的CIDR来确定IP的数量，只对于Flannel网络类型集群生效。

默认值：25。 |
|kubernetes\_version|String|Body|否|1.16.9-aliyun.1|集群版本，与Kubernetes社区基线版本保持一致。建议选择最新版本，若不指定，默认使用最新版本。目前您可以在ACK控制台创建两种最新版本的集群。您可以通过API创建其他Kubernetes版本集群。关于ACK支持的Kubernetes版本，请参见[Kubernetes版本发布概览](/intl.zh-CN/新功能发布记录/Kubernetes版本发布说明/Kubernetes版本发布概览.md)。 |
|deletion\_protection|Boolean|Body|否|true|集群是否开启集群删除保护，防止通过控制台或API误删除集群。取值：

-   `true`：集群开启集群删除保护。
-   `false`：集群不开启集群删除保护。

默认值：`false`。 |

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

返回示例

`XML格式`

```
<cluster_id>cb95aa626a47740afbf6aa099b65****</cluster_id>
<task_id>687C5BAA-D103-4993-884B-C35E4314A1E1</task_id>
<request_id>T-5a54309c80282e39ea00002f</request_id>
```

JSON格式

```
{
    "cluster_id": "cb95aa626a47740afbf6aa099b65****",
    "request_id": "687C5BAA-D103-4993-884B-C35E4314A1E1",
    "task_id": "T-5a54309c80282e39ea00002f"
}
```

## 错误码

访问[错误中心](https://error-center.alibabacloud.com/status/product/CS)查看更多错误码。

