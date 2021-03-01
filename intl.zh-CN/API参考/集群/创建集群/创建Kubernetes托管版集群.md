# 创建Kubernetes托管版集群

调用CreateCluster创建一个Kubernetes托管版集群实例，并创建指定数量的节点。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=CS&api=CreateCluster&type=ROA&version=2015-12-15)

## 请求语法

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
  "deletion_protection" : Boolean,
  "disable_rollback" : Boolean,
  "timeout_mins" : Long
}
```

## 请求参数

|名称|类型|是否必选|示例值|描述|
|--|--|----|---|--|
|cluster\_type|String|是|ManagedKubernetes|集群类型。取值`ManagedKubernetes`创建标准托管版集群。|
|key\_pair|String|是|secrity-key|密钥对名称，和`login_password`二选一。|
|login\_password|String|是|Hello@1234|SSH登录密码，和`key_pair`二选一。密码规则为8~30个字符，且至少同时包含三项（大小写字母、数字和特殊符号）。|
|name|String|是|cluster-demo|集群名称。

命名规则：由数字、汉字、英文字符或短划线（-）组成，长度范围1~63个字符，且不能以短划线（-）开头。 |
|num\_of\_nodes|Long|是|3|Worker节点数。范围是\[0,100\]。|
|region\_id|String|是|cn-beijing|集群所在地域ID。|
|snat\_entry|Boolean|否|true|是否为网络配置SNAT：

-   当已有VPC能访问公网环境时，设置为`false`。
-   当已有VPC不能访问公网环境时：
    -   设置为`true`，表示配置SNAT，此时可以访问公网环境。
    -   设置为`false`，表示不配置SNAT，此时不能访问公网环境。

如果您的应用需要访问公网，建议配置为true。

默认值：`false`。 |
|vswitch\_ids|Array of String|是|\["vsw-2ze48rkq464rsdts1\*\*\*\*"\]|交换机ID。List长度范围为\[1,3\]。|
|worker\_system\_disk\_category|String|是|cloud\_efficiency|Worker节点系统盘类型，取值：

-   `cloud_efficiency`：高效云盘。
-   `cloud_ssd`：SSD云盘。

默认值：`cloud_ssd`。 |
|worker\_system\_disk\_size|Long|是|120|Worker节点系统盘大小，单位为GiB。

取值范围：\[40,500\]。

该参数的取值必须大于或者等于max\{40, ImageSize\}。

默认值：120。 |
|addons|Array of [addon](/intl.zh-CN/API参考/通用数据结构.md)|否|\[\{"name": "terway-eniip","config": ""\}, \{"name": "logtail-ds","config": "\{\\"IngressDashboardEnabled\\":\\"true\\",\\"sls\_project\_name\\":\\"your\_sls\_project\_name\\"\}"\}, \{"name":"nginx-ingress-controller","config":"\{\\"IngressSlbNetworkType\\":\\"internet\\"\}"\}\]|Kubernetes集群安装的组件列表。组件的结构包括：

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
|cluster\_spec|String|否|ack.pro.small|托管版集群类型，面向托管集群。取值：

-   `ack.pro.small`：专业托管集群，即：”ACK Pro版集群“。
-   `ack.standard`：标准托管集群。

默认值：`ack.standard`。取值可以为空，为空时则创建标准托管集群。

更多详情，请参见[t1913636.dita\#concept\_2558837](/intl.zh-CN/Kubernetes集群用户指南/ACK Pro集群/ACK Pro版集群介绍.md)。 |
|encryption\_provider\_key|String|否|0fe64791-55eb-4fc7-84c5-c6c7cdca\*\*\*\*|KMS密钥ID，使用该密钥对数据盘进行加密。更多详情，请参见[t22664.dita\#concept\_28935\_zh](/intl.zh-CN/产品简介/什么是密钥管理服务.md)。

**说明：** 该功能只在专业托管版集群（ACK Pro版集群）中生效。 |
|container\_cidr|String|是|172.20.0.0/16|Pod网络地址段，不能和VPC网段冲突。当选择系统自动创建VPC时，默认使用172.16.0.0/16网段。

**说明：** 当创建Flannel网络类型的集群时，该字段为必填。 |
|cloud\_monitor\_flags|Boolean|否|true|集群是否安装云监控插件。取值：

-   `true`：安装云监控插件。
-   `false`：不安装云监控插件。

默认值：`false`。 |
|disable\_rollback|Boolean|否|true|集群创建失败是否回滚。取值：

-   `true`：当集群创建失败时，进行回滚操作。
-   `false`：当集群创建失败时，不进行回滚操作。

默认值：`false`。 |
|endpoint\_public\_access|Boolean|否|true|是否开启公网访问。通过EIP暴露API Server，实现集群公网访问。取值： -   `true`：开启公网访问。
-   `false`：不开启公网访问。选择不开放时，则无法通过外网访问集群API Server。

默认值：`true`。 |
|proxy\_mode|String|否|ipvs|kube-proxy代理模式，支持`iptables`和`ipvs`两种模式，默认为`ipvs`。 |
|security\_group\_id|String|否|sg-bp1bdue0qc1g7k1e\*\*|使用已有安全组创建集群时需要指定安全组ID，和`is_enterprise_security_group`二选一，集群节点自动加入到此安全组。 |
|is\_enterprise\_security\_group|Boolean|否|true|自动创建企业级安全组，当`security_group_id`为空的时生效。

**说明：** Terway网络类型集群，必须指定为企业安全组。

-   `true`：创建企业级安全组。
-   `false`：不创建企业级安全组。

默认值：`false`。 |
|service\_cidr|String|是|172.21.0.0/20|Service网络地址段，不能和VPC网段及Pod网络网段冲突。当选择系统自动创建VPC时，默认使用172.19.0.0/20网段。 |
|tags|Array of [tag](/intl.zh-CN/API参考/通用数据结构.md)|否|\[\{"key": "env", "value": "prod"\}\]|给集群打tag标签。包含以下信息：

-   `key`：标签名称。
-   `value`：标签值。 |
|timezone|String|否|Asia/Shanghai|集群使用的时区。 |
|taints|Array of [taint](/intl.zh-CN/API参考/通用数据结构.md)|否|\[\{"key": "env", "value": "private", "effect": "NoSchedule"\}\]|节点污点信息。污点和容忍度（Toleration）相互配合，可以用来避免Pod被分配到不合适的节点上。更多信息，请参见[taint-and-toleration](https://kubernetes.io/zh/docs/concepts/scheduling-eviction/taint-and-toleration/)。 |
|cluster\_domain|String|否|cluster.local|集群本地域名。

命名规则：域名由小数点（.）分隔的一个或多个部分构成，每个部分最长为63个字符，可以使用小写字母、数字和短划线（-），且首尾必须为小写字母或数字。 |
|custom\_san|String|否|cs.aliyun.com|自定义证书SAN，多个IP或域名以英文逗号（,）分隔。 |
|service\_account\_issuer|String|否|kubernetes.default.svc|ServiceAccount是Pod和集群`apiserver`通讯的访问凭证。而`service-account-issuer`是`serviceaccount token`中的签发身份，即`token payload`中的`iss`字段。

关于`ServiceAccount`更多详情，请参见[t1881266.dita\#task\_2460323](/intl.zh-CN/Kubernetes集群用户指南/安全管理/部署服务账户令牌卷投影.md)。 |
|api\_audiences|String|否|kubernetes.default.svc|ServiceAccount是Pod和集群`apiserver`通讯的访问凭证，而`api-audiences`是合法的请求`token`身份，用于`apiserver`服务端认证请求`token`是否合法。支持配置多个`audienc`e，通过英文逗号（,）分割。

关于`ServiceAccount`更多详情，请参见[t1881266.dita\#task\_2460323](/intl.zh-CN/Kubernetes集群用户指南/安全管理/部署服务账户令牌卷投影.md)。 |
|node\_name\_mode|String|否|aliyun.com00055test|自定义节点名称。

节点名称由三部分组成：前缀+节点IP地址子串+后缀：

-   前缀和后缀均可由英文句号（.）分隔的一个或多个部分构成，每个部分可以使用小写字母、数字和短划线（-），且首尾必须为小写字母和数字。
-   IP地址段长度指截取节点IP地址末尾的位数，取值范围\[5,12\]。

例如，节点IP地址为：192.168.0.55，指定前缀为aliyun.com，IP地址段长度为5，后缀为test，则节点名称为aliyun.com00055test。 |
|rds\_instances|Array of String|否|rm-2zev748xi27xc\*\*\*\*|RDS实例名称。 |
|image\_id|String|否|m-bp16z7xko3vvv8gt\*\*\*\*|节点自定义镜像，默认使用系统镜像。当选择自定义镜像时，将取代默认系统镜像。请参见[t1849273.dita\#task\_2362493](/intl.zh-CN/最佳实践/自建Kubernetes迁移ACK/使用自定义镜像创建ACK集群.md)。 |
|pod\_vswitch\_ids|Array of String|是|vsw-2ze97jwri7cei0mpw\*\*\*\*|Terway网络类型的集群，需要指定Pod所在的虚拟交换，因为Pod独占一个机器IP。 |
|instances|Array of String|否|i-2ze4zxnm36vq00xn\*\*\*\*|实例名称。 |
|format\_disk|Boolean|否|false|使用已有实例创建集群时，是否对实例进行数据盘挂载，取值：

-   `true`：将容器和镜像存储在数据盘，数据盘内原有数据将丢失，请注意备份数据。
-   `false`：不将容器和镜像存储在数据盘。

默认值：`false`。

数据盘挂载规则：

-   如果ECS已挂载数据盘，且最后一块数据盘的文件系统未初始化，系统会自动将该数据盘格式化为ext4，用来存放内容/var/lib/docker、/var/lib/kubelet。
-   如果ECS未挂载数据盘，则不会挂载新的数据盘。 |
|keep\_instance\_name|Boolean|否|true|使用已有实例创建集群时，是否保留实例名称。

-   `true`：保留。
-   `false`：不保留，会用系统规则进行替换。

默认值：`true`。 |
|timeout\_mins|Long|否|60|集群创建超时时间，单位分钟。

默认值：60。 |
|vpcid|String|是|vpc-2zeik9h3ahvv2zz95\*\*\*\*|集群使用的VPC实例ID。 |
|worker\_auto\_renew|Boolean|否|true|Worker节点是否开启自动续费，当`worker_instance_charge_type`取值为`PrePaid`时才生效，可选值为：

-   `true`：自动续费。
-   `false`：不自动续费。

默认值：`true`。 |
|worker\_auto\_renew\_period|Long|否|1|Worker节点自动续费周期，当选择包年包月付费类型时才生效，且为必选值。

取值范围：\{1, 2, 3, 6, 12\}。 |
|worker\_data\_disks|Array of [data\_disk](/intl.zh-CN/API参考/通用数据结构.md)|否|\[\{"category": "cloud\_ssd", "size": "40", "auto\_snapshot\_policy\_id": "sp-bp14j6w7ss6ozzbp\*\*"\}\]|Worker数据盘类型、大小等配置的组合。|
|worker\_data\_disk\_category|String|否| |数据盘类型。取值：-   `cloud_efficiency`：高效云盘。
-   `cloud_ssd`：SSD云盘。
-   `cloud`：普通云盘。

默认值：`cloud_efficiency`。**说明：** 过期参数，替代参数请参见worker\_data\_disks参数中的category取值。 |
|worker\_data\_disk\_size|Long|否| |数据盘大小，单位为GiB。**说明：** 过期参数，替代参数请参见worker\_data\_disks参数中的size取值。 |
|worker\_instance\_charge\_type|String|否|PrePaid|Worker节点付费类型，取值：

-   `PrePaid`：包年包月。
-   `PostPaid`：按量付费。

默认值：按量付费。 |
|worker\_period|Long|否|1|Worker节点包年包月时长，当`worker_instance_charge_type`取值为`PrePaid`时才生效且为必选值。

取值范围：\{1, 2, 3, 6, 12, 24, 36, 48, 60\}。

默认值：1。 |
|worker\_period\_unit|String|否|Month|Wroker节点付费周期，当付费类型为`PrePaid`的时候需要指定周期。

取值：`Month`，当前只支持以月为周期。 |
|worker\_instance\_types|Array of String|是|\["ecs.n4.xlarge"\]|Worker节点实例规格，至少要指定一个实例规格。更多详细信息，请参见[实例规格族](/intl.zh-CN/实例/实例规格族.md)。

**说明：** 实例规格优先级随着在数据中的位置增大依次降低。当无法根据优先级较高的实例规格创建出实例时，会自动选择下一优先级的实例规格来创建实例。 |
|cpu\_policy|String|否|none|节点CPU管理策略。当集群版本在1.12.6及以上时支持以下两种策略：

-   `static`：允许为节点上具有某些资源特征Pod增强其CPU亲和性和独占性。
-   `none`：表示启用现有的默认CPU亲和性方案。

默认值：`none`。 |
|runtime|[runtime](/intl.zh-CN/API参考/通用数据结构.md)|否|\{"name": "docker", "version": "19.03.5"\}|容器运行时，支持`containerd`、`docker`、`Sandboxed-Container.runv`三种运行时，默认为`docker`。runtime包括以下2个信息： -   `name`：容器运行时名称。
-   `version`：容器运行时版本。

有关容器运行时的选择，请参见[t1879872.dita\#task\_2455499](/intl.zh-CN/Kubernetes集群用户指南/安全沙箱/如何选择Docker运行时与安全沙箱运行时？.md)。 |
|platform|String|否|CentOS|操作系统发行版。取值：

-   `CentOS`
-   `AliyunLinux`
-   `QbootAliyunLinux`
-   `Qboot`
-   `Windows`
-   `WindowsCore`

默认值：`CentOS`。 |
|user\_data|String|否|IyEvdXNyL2Jpbi9iYXNoCmVjaG8gIkhlbGxvIEFD\*\*\*\*|自定义节点数据。更多详情，请参见[t9660.dita\#concept\_fgf\_tjn\_xdb](/intl.zh-CN/实例/管理实例/使用实例自定义数据/生成实例自定义数据.md)。 |
|os\_type|String|否|Linux|操作系统平台类型。取值：

-   `Windows`
-   `Linux`

默认值：`Linux`。 |
|node\_cidr\_mask|String|否|25|节点IP数量，通过指定网络的CIDR来确定IP的数量，只对于Flannel网络类型集群生效。

默认值：25。 |
|kubernetes\_version|String|否|1.16.9-aliyun.1|集群版本，与Kubernetes社区基线版本保持一致。建议选择最新版本，若不指定，默认使用最新版本。目前您可以在ACK控制台创建两种最新版本的集群。您可以通过API创建其他Kubernetes版本集群。关于ACK支持的Kubernetes版本，请参见[Kubernetes版本发布概览](/intl.zh-CN/新功能发布记录/Kubernetes版本发布说明/Kubernetes版本发布概览.md)。 |
|deletion\_protection|Boolean|否|true|集群是否开启集群删除保护，防止通过控制台或API误删除集群。取值：

-   `true`：集群开启集群删除保护。
-   `false`：集群不开启集群删除保护。

默认值：`false`。 |

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

## 创建ACK标准托管版集群示例

请求示例

```
POST /clusters 
<公共请求头>
{
    "name":"ACK托管版",                      // 集群名称 #required
    "cluster_type":"ManagedKubernetes",     // 集群类型 #required
    "disable_rollback":true,                // 失败是否回滚。
    "timeout_mins":60,                      // 集群创建超时时间。
    "kubernetes_version":"1.18.8-aliyun.1", // 集群版本，只维护最新的2个版本。
    "region_id":"cn-zhangjiakou",  // 地域ID, #required。
    "snat_entry":true,             // 为专有网络配置SNAT规则，以开启集群公网访问。
    "cloud_monitor_flags":true,    // 在ECS节点上安装云监控插件。
    "endpoint_public_access":true, // 开启公网访问。
    "deletion_protection":true,    // 集群删除保护。
    "node_cidr_mask":"26",         // 节点IP数量，通过指定节点网段的掩码来决定。
    "proxy_mode":"ipvs",           // kube-proxy代理模式，取值：iptables或者ipvs。
    "tags":[                       // 集群标签。标签将同时作用于 ACK集群、ECS实例和Kuberntes节点。
        {
            "key":"tag-k",
            "value":"tag-v"
        }
    ],
    "timezone":"Asia/Shanghai",   // 时区
    "addons":[                    // 组件配置
        {
            "name":"flannel"      // 当集群网络类型是Terway时，设置为{"name":"terway-eni"}。
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
            "name":"nginx-ingress-controller",                      // 组件名称
            "config":"{\"IngressSlbNetworkType\":\"internet\"}",    // 组件配置
            "disabled": true                                        // 是否禁止默认安装。
        },
        {
            "name":"arms-prometheus"
        }
    ],
    "cluster_spec":"ack.pro.small",       // 托管版集群类型。ack.pro.small：ACK Pro版集群；ack.standed：标准托管版集群。
    "encryption_provider_key":"8734596c-c0d6-4a63-a76e-fe72c7b0****", // Secret落盘加密的密钥ID。
    "os_type":"Linux",        // 操作系统类型，支持：Linux、Windows。
    "platform":"AliyunLinux", // 操作系统平台，支持：CentOS，AliyunLinux，Windows, WindowsCore。
    "user_data":"IyEvdXNyL2Jpbi9iYXNoCmVjaG8gIkhlbGxvIEFDSyEi", // 节点自定义数据。
    "runtime":{             // 容器运行时
        "name":"docker",    // 运行时名称
        "version":"19.03.5" // 运行时版本
    },
    "worker_instance_types":[  // Worker节点实例规格#required
        "ecs.t6-c1m4.large"
    ],
    "num_of_nodes":3,                              // Worker节点数量 #required
    "worker_system_disk_category":"cloud_essd",    // Worker节点系统盘类型 #required
    "worker_system_disk_size":120,                 // Worker节点系统盘大小 #required
    "worker_data_disks":[                          // Worker节点数据盘配置
        {
            "category":"cloud_efficiency",        // 数据盘类型。
            "size":"40",                          // 数据盘大小，取值：40～32768。
            "encrypted":"true",                   // 数据盘是否加密。
            "auto_snapshot_policy_id":"sp-8vbajx6y2hk21hco****", // 数据盘备份策略ID。
        }
    ],
    "worker_instance_charge_type":"PrePaid",      // Worker节点付费类型：PostPaid或PrePaid。
    "worker_period_unit":"Month",                 // Worker节点自动续费周期，只支持：Month。
    "worker_period":1,                            // Worker节点自动续费时长，默认：1。
    "worker_auto_renew":true,                     // Worker节点到期是否自动续费。
    "worker_auto_renew_period":1,                 // Worker节点到期后自动续费周期。
    "vpcid":"vpc-8vbh3b9a2f38urhls****",          // 集群专有网络ID。 #required
    "container_cidr":"172.20.0.0/16",             // 集群POD网段#required, 网络模式是terway插件的情况下不必须。
    "service_cidr":"172.21.0.0/20",               // 集群Service网段。 #required
    "vswitch_ids":[                               // 集群虚拟交换机ID。 #required
        "vsw-8vbmoffowsztjaawj****"
    ],
    "login_password":"Hello1234",                 // 集群节点Root用户登录密码，与key_pair二选一。 #required
    "key_pair": "sin-name",                       // 集群节点Root免密登录密钥，与login_password二选一。 #required
    "cpu_policy":"none",                 // 集群节点CPU亲和策略。取值：static、none。
    "taints":[                           // 节点污点配置。
        {
            "key":"1",                   // 污点key值。
            "value":"1",                 // 污点value值。
            "effect":"NoSchedule"        // 污点调度策略。
        }
    ],
    "cluster_domain":"cluster.local",    // 集群本地域名，默认：cluster.local。
    "custom_san":"cs.aliyuncs.com",      // 集群自定义证书SAN。
    "service_account_issuer":"kubernetes.default.svc", // 服务账户令牌卷投影。serviceaccount token中的签发身份，即token payload中的iss字段。
    "api_audiences":"kubernetes.default.svc",          // 服务账户令牌卷投影。合法的请求token身份，用于apiserver服务端认证请求token是否合法。
    "node_name_mode":"customized,aliyun.com,5,k8s",    // 自定义集群节点名称。
    "security_group_id":"sg-8vb7grbyvlb10j0i****",     // 使用已有安全组。和is_enterprise_security_group二选一。
    "is_enterprise_security_group":true,               // 自动创建企业安全组，和security_group_id二选一。
    "rds_instances": ["rm-xx","rm-xx"],                // RDS白名单。
    "image_id":"CentOS-xxx",                           // 自定义节点系统镜像。
    "pod_vswitch_ids":[                                // Terway网络类型的集群，需要指定POD所在的虚拟交换，因为POD独占一个机器IP。                     
        "vsw-8vbo5fwyqiw0bbtlq****"
    ],
    "instances": [                // 使用已有实例创建集群时，Worker节点列表。
        "i-dewgagxdfa****",
        "i-3kjaf9q43l****"
    ],
    "format_disk": false,        // 是否进行数据盘挂载，数据盘挂载会格式化数据盘，请注意做数据备份。
    "keep_instance_name": true   // 是否保留原实例名称，默认：保留。
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

