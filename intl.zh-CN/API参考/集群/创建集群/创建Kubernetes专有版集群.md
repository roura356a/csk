# 创建Kubernetes专有版集群

调用CreateCluster创建一个标准Kubernetes专有版集群。

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

## 请求参数

|名称|类型|是否必选|示例值|描述|
|--|--|----|---|--|
|name|String|是|cluster-demo|集群名称。

 命名规则：由数字、汉字、英文字符或短划线（-）组成，长度范围1~63个字符，且不能以短划线（-）开头。 |
|region\_id|String|是|cn-beijing|集群所在的地域ID。 |
|cluster\_type|String|是|Kubernetes|集群类型。取值：

 -   `Kubernetes`：专有版集群。
-   `ManagedKubernetes`：标准托管版集群、边缘托管版集群。
-   `Ask`：标准Serverless集群。
-   `ExternalKubernetes`：注册至ACK的外部集群。 |
|kubernetes\_version|String|否|1.16.9-aliyun.1|集群版本，与Kubernetes社区基线版本保持一致。建议选择最新版本，若不指定，默认使用最新版本。

 目前您可以在ACK控制台创建两种最新版本的集群。您可以通过API创建其他Kubernetes版本集群。关于ACK支持的Kubernetes版本，请参见[t1960241.dita\#task\_1960241](/intl.zh-CN/新功能发布记录/Kubernetes版本发布说明/Kubernetes版本发布概览.md)。 |
|runtime|[runtime](/intl.zh-CN/API参考/通用数据结构.md)|否| |集群内容器运行时，默认采用Docker，同时我们还还支持安全沙箱容器。

 更多有关容器引擎的选择，请参见[t1879872.dita\#task\_2455499](/intl.zh-CN/Kubernetes集群用户指南/安全沙箱管理/如何选择Docker运行时与安全沙箱运行时？.md)。 |
|vpcid|String|是|vpc-2zeik9h3ahvv2zz95\*\*\*\*|集群使用的专有网络，创建集群时必须为集群提供。 |
|pod\_vswitch\_ids|Array of String|否|vsw-2ze97jwri7cei0mpw\*\*\*\*|Pod虚拟交换机列表，您需要为每一个节点虚拟交换机指定至少一个相同可用区的Pod虚拟交换机并且不能跟节点`vswitch`重复，建议选择网段掩码不大于19的虚拟交换机。

 **说明：** 当集群采用Terway网络类型时，必须为集群指定`pod_vswitch_ids`。 |
|container\_cidr|String|是|172.20.0.0/16|Pod网络地址段，不能和VPC网段冲突。当选择系统自动创建VPC时，默认使用172.16.0.0/16网段。

 **说明：**

-   当创建Flannel网络类型的集群时，该字段为必填。
-   当创建Terway网络类型的集群时，该字段不需要填。 |
|service\_cidr|String|是|172.21.0.0/20|Service网络地址段，可选范围：10.0.0.0/16-24，172.16-31.0.0/16-24，192.168.0.0/16-24

 不能与VPC网段10.1.0.0/21及VPC内已有Kubernetes集群使用的网段重复，创建成功后不能修改。

 默认使用172.19.0.0/20网段。 |
|security\_group\_id|String|否|sg-bp1bdue0qc1g7k\*\*\*\*|使用已有安全组创建集群时需要指定安全组ID，和`is_enterprise_security_group`二选一，集群节点自动加入到此安全组。 |
|is\_enterprise\_security\_group|Boolean|否|true|自动创建企业级安全组，当`security_group_id`为空的时生效。

 **说明：** 使用普通安全组时，集群内节点与Terway Pod数量之和不能超过2000。所以创建Terway网络类型集群时，建议使用企业安全组。

 -   `true`：创建并使用企业级安全组。
-   `false`：不使用企业级安全组。

 默认值：`true`。 |
|snat\_entry|Boolean|否|true|为专有网络配置SNAT。取值：

 -   true：将为您创建NAT网关并自动配置SNAT规则，若您集群内的节点、应用等需要访问公网需要设置为`true`。
-   false：不为您创建NAT网关及SNAT规则。这种模式下，集群内节点及应用将不能访问公网。

 **说明：** 如果创建集群时未开启，后续业务需要访问公网，可[t1936294.dita\#task\_1936294](/intl.zh-CN/Kubernetes集群用户指南/集群管理/连接集群/如何为已有集群开启SNAT？.md)。

 默认值：true。 |
|endpoint\_public\_access|Boolean|否|true|是否开启公网访问。通过EIP暴露API Server，实现集群公网访问。

 -   `true`：开启公网访问。
-   `false`：不开启公网访问。选择不开放时，则无法通过外网访问集群API Server。

 默认值：`false`。 |
|ssh\_flags|Boolean|否|true|是否开放公网SSH登录。用登录”专有版集群“的Master节点，托管版集群中该参数不生效。

 -   `true`：表示开放。
-   `false`：表示不开放。

 默认值：`false`。 |
|timezone|String|否|Asia/Shanghai|集群使用的时区。 |
|node\_cidr\_mask|String|否|25|节点IP数量，通过指定网络的CIDR来确定IP的数量，只对于Flannel网络类型集群生效。

 默认值：`26`。 |
|user\_ca|String|否|-----BEGIN CERTIFICATE-----\*\*\*\*|自定义集群CA。 |
|user\_data|String|否|IyEvdXNyL2Jpbi9iYXNoCmVjaG8gIkhlbGxvIEFD\*\*\*\*|自定义节点数据。更多详情，请参见[t9660.dita\#concept\_fgf\_tjn\_xdb](/intl.zh-CN/实例/管理实例/使用实例自定义数据/生成实例自定义数据.md)。 |
|cluster\_domain|String|否|cluster.local|集群本地域名。

 命名规则：域名由小数点（.）分隔的一个或多个部分构成，每个部分最长为63个字符，可以使用小写字母、数字和短划线（-），且首尾必须为小写字母或数字。 |
|node\_name\_mode|String|否|aliyun.com00055test|自定义节点名称。

 节点名称由三部分组成：前缀+节点IP地址子串+后缀：

 -   前缀和后缀均可由英文句号（.）分隔的一个或多个部分构成，每个部分可以使用小写字母、数字和短划线（-），且首尾必须为小写字母和数字。
-   IP地址段长度指截取节点IP地址末尾的位数，取值范围\[5,12\]。

 例如，节点IP地址为：192.168.0.55，指定前缀为aliyun.com，IP地址段长度为5，后缀为test，则节点名称为aliyun.com00055test。 |
|custom\_san|String|否|cs.aliyun.com|自定义证书SAN，多个IP或域名以英文逗号（,）分隔。 |
|service\_account\_issuer|String|否|kubernetes.default.svc|ServiceAccount是Pod和集群`apiserver`通讯的访问凭证。而`service-account-issuer`是`serviceaccount token`中的签发身份，即`token payload`中的`iss`字段。

 关于`ServiceAccount`更多详情，请参见[t1881266.dita\#task\_2460323](/intl.zh-CN/Kubernetes集群用户指南/安全管理/部署服务账户令牌卷投影.md)。 |
|api\_audiences|String|否|kubernetes.default.svc|ServiceAccount是Pod和集群`apiserver`通讯的访问凭证，而`api-audiences`是合法的请求`token`身份，用于`apiserver`服务端认证请求`token`是否合法。支持配置多个`audienc`e，通过英文逗号（,）分割。

 关于`ServiceAccount`更多详情，请参见[t1881266.dita\#task\_2460323](/intl.zh-CN/Kubernetes集群用户指南/安全管理/部署服务账户令牌卷投影.md)。 |
|image\_id|String|否|m-bp16z7xko3vvv8gt\*\*\*\*|节点自定义镜像，默认使用系统镜像。当选择自定义镜像时，将取代默认系统镜像。请参见[t1849273.dita\#task\_2362493](/intl.zh-CN/最佳实践/自建Kubernetes迁移ACK/使用自定义镜像创建ACK集群.md)。 |
|rds\_instances|Array of String|否|rm-2zev748xi27xc\*\*\*\*|RDS实例名称。 |
|tags|Array of [tag](/intl.zh-CN/API参考/通用数据结构.md)|否| |节点标签。标签定义规则：

 -   标签由区分大小写的键值对组成，您最多可以设置20个标签。
-   标签键不可以重复，最长为64个字符；标签值可以为空，最长为128个字符。标签键和标签值都不能以“aliyun”、“acs:”、“https://”或“http://”开头。详情请参见[Labels and Selectors](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set)。 |
|addons|Array of [addon](/intl.zh-CN/API参考/通用数据结构.md)|否| |集群组件列表，创建集群时通过`addons`指定想要安装的集群组件。

 **网络组件**：必选，包含Flannel和Terway两种网络类型，创建集群时二选一：

 -   Flannel网络：\[\{"name":"flannel","config":""\}\]。
-   Terway网络：\[\{"name": "terway-eniip","config": ""\}\] 。

 **存储组件**：必选，支持`csi`和`flexvolume`两种类型：

 -   `csi`：\[\{"name":"csi-plugin","config": ""\},\{"name": "csi-provisioner","config": ""\}\]。
-   `flexvolume`：\[\{"name": "flexvolume","config": ""\}\] 。

 **日志组件**：可选。推荐开启，如果不开启日志服务时，将无法使用集群审计功能。

 -   使用已有`SLS Project`：\[\{"name": "logtail-ds","config": "\{\\"IngressDashboardEnabled\\":\\"true\\",\\"sls\_project\_name\\":\\"your\_sls\_project\_name\\"\}"\}\] 。
-   创建新的`SLS Project`：\[\{"name": "logtail-ds","config": "\{\\"IngressDashboardEnabled\\":\\"true\\"\}"\}\] 。

 **Ingress组件**：可选，ACK专有版集群默认安装Ingress组件`nginx-ingress-controller`。

 -   安装Ingress并且开启公网：\[\{"name":"nginx-ingress-controller","config":"\{\\"IngressSlbNetworkType\\":\\"internet\\"\}"\}\] 。
-   禁止默认安装Ingress：\[\{"name": "nginx-ingress-controller","config": "","disabled": true\}\] 。

 **事件中心**：可选，默认开启。

 事件中心提供对Kubernetes事件的存储、查询、告警等能力。Kubernetes事件中心关联的Logstore在90天内免费。关于免费策略的更多信息，请参见[t1857672.dita\#task\_2389213](/intl.zh-CN/应用中心（App）/K8S事件中心/创建并使用Kubernetes事件中心.md)。

 开启事件中心：\[\{"name":"ack-node-problem-detector","config":"\{\\"sls\_project\_name\\":\\"your\_sls\_project\_name\\"\}"\}\]。 |
|taints|Array of [taint](/intl.zh-CN/API参考/通用数据结构.md)|否| |节点污点信息。污点和容忍度（Toleration）相互配合，可以用来避免Pod被分配到不合适的节点上。更多信息，请参见[taint-and-toleration](https://kubernetes.io/zh/docs/concepts/scheduling-eviction/taint-and-toleration/)。 |
|cloud\_monitor\_flags|Boolean|否|true|集群是否安装云监控插件。取值：

 -   `true`：安装云监控插件。
-   `false`：不安装云监控插件。

 默认值：`false`。 |
|platform|String|否|CentOS|操作系统发行版。取值：

 -   CentOS
-   AliyunLinux
-   QbootAliyunLinux
-   Qboot
-   Windows
-   WindowsCore

 默认值：`AliyunLinux`。 |
|os\_type|String|否|Linux|操作系统平台类型。取值：

 -   Windows
-   Linux

 默认值：`Linux`。 |
|cpu\_policy|String|否|none|节点CPU管理策略。当集群版本在1.12.6及以上时支持以下两种策略：

 -   `static`：允许为节点上具有某些资源特征Pod增强其CPU亲和性和独占性。
-   `none`：表示启用现有的默认CPU亲和性方案。

 默认值：`none`。 |
|proxy\_mode|String|否|ipvs|kube-proxy代理模式

 -   `iptables`: 成熟稳定的kube-proxy代理模式，Kubernetes Service的服务发现和负载均衡使用iptables规则配置，但是性能一般，受规模影响较大，适用于集群存在少量的service。
-   `ipvs`：高性能的kube-proxy代理模式，Kubernetes Service的服务发现和负载均衡使用Linux IPVS模块进行配置，适用于集群存在大量的Service，对负载均衡有高性能要求的场景。

 默认值：`ipvs`。 |
|node\_port\_range|String|否|30000~32767|节点服务端口，可选端口范围：\[30000,65535\]。

 默认值：`30000-32767`。 |
|key\_pair|String|是|secrity-key|密钥对名称，和`login_password`二选一。 |
|login\_password|String|是|Hello@1234|SSH登录密码，和`key_pair`二选一。密码规则为8~30个字符，且至少同时包含三项（大小写字母、数字和特殊符号）。 |
|master\_count|Long|否|3|Master节点数量，可选值`3`或`5`。

 默认值：`3`。 |
|master\_vswitch\_ids|Array of String|是|vsw-2ze3ds0mdip0hdz8i\*\*\*\*|虚拟交换ID。 |
|master\_instance\_types|Array of String|是|ecs.n4.xlarge|Master节点实例类型。 |
|master\_system\_disk\_category|String|是|cloud\_ssd|Master节点系统盘类型，取值：

 -   `cloud_efficiency`：高效云盘。
-   `cloud_ssd`：SSD云盘。
-   `cloud_essd`：ESSD云盘。

 默认值：`cloud_ssd`。不同可用区，默认值可能会有所差异。 |
|master\_system\_disk\_size|Long|是|120|Master节点系统盘类型，取值范围\[40,500\]，单位：GiB。

 默认值：`120`。 |
|master\_system\_disk\_snapshot\_policy\_id|String|否|sp-2zej1nogjvovnz4z\*\*\*\*|Master节点系统盘采用的自动快照策略ID。 |
|master\_instance\_charge\_type|String|否|PrePaid|Master节点付费类型，取值：

 -   `PrePaid`：包年包月。
-   `PostPaid`：按量付费。

 默认值：`PostPaid`。 |
|master\_period\_unit|String|否|Month|Master节点付费周期，当付费类型为`PrePaid`的时候必须指定周期。

 取值：`Month`，当前仅支持以月为周期。 |
|master\_period|Long|否|1|Master节点包年包月时长，当`master_instance_charge_type`取值为`PrePaid`时才生效且为必选值。

 取值范围：\{1, 2, 3, 6, 12, 24, 36, 48, 60\}。

 默认值：1。 |
|master\_auto\_renew|Boolean|否|true|Master节点是否开启自动续费，当`master_instance_charge_type`取值为`PrePaid`时才生效，可选值为：

 -   `true`：自动续费。
-   `false`：不自动续费。

 默认值：`true`。 |
|master\_auto\_renew\_period|Long|否|1|Master节点自动续费周期，当选择包年包月付费类型时才生效，且为必选值。

 取值范围：\{1, 2, 3, 6, 12\}。

 默认值：1。 |
|num\_of\_nodes|Long|是|3|Worker节点数。范围是\[0，100\]。 |
|worker\_vswitch\_ids|Array of String|是|vsw-2ze3ds0mdip0hdz8i\*\*\*\*|虚拟交换机ID列表，取值范围：\[1,20\]。为保证高可用性，建议选择不同可用区的虚拟交换机。 |
|worker\_instance\_types|Array of String|是|ecs.n4.large|Worker节点实例规格，至少要指定一个实例规格。更多信息，请参见[t9548.dita\#concept\_sx4\_lxv\_tdb](/intl.zh-CN/实例/实例规格族.md)。

 **说明：** 实例规格优先级随着在数据中的位置增大依次降低。当无法根据优先级较高的实例规格创建出实例时，会自动选择下一优先级的实例规格来创建实例。 |
|worker\_system\_disk\_category|String|是|cloud\_efficiency|Worker节点系统盘类型，取值：

 -   `cloud_efficiency`：高效云盘。
-   `cloud_ssd`：SSD云盘。

 默认值：`cloud_ssd`。 |
|worker\_system\_disk\_size|Long|是|120|Worker节点系统盘大小，单位为GiB。

 取值范围：\[40,500\]。

 该参数的取值必须大于或者等于max\{40, ImageSize\}。

 默认值：120。 |
|worker\_system\_disk\_snapshot\_policy\_id|String|否|sp-2zej1nogjvovnz4z\*\*\*\*|Worker节点系统盘采用的自动快照策略ID。 |
|worker\_data\_disks|Array of [data\_disk](/intl.zh-CN/API参考/通用数据结构.md)|否| |Worker节点数据盘类型、大小等配置的组合。 |
|worker\_instance\_charge\_type|String|否|PrePaid|Worker节点付费类型，取值：

 -   `PrePaid`：包年包月。
-   `PostPaid`：按量付费。

 默认值：按量付费。 |
|worker\_period\_unit|String|否|Month|Wroker节点付费周期，当付费类型为`PrePaid`的时候需要指定周期。

 取值：`Month`，当前只支持以月为周期。 |
|worker\_period|Long|否|1|Worker节点包年包月时长，当`worker_instance_charge_type`取值为`PrePaid`时才生效且为必选值。

 取值范围：\{1, 2, 3, 6, 12, 24, 36, 48, 60\}。

 默认值：1。 |
|worker\_auto\_renew|Boolean|否|true|Worker节点是否开启自动续费，当`worker_instance_charge_type`取值为`PrePaid`时才生效，取值：

 -   `true`：自动续费。
-   `false`：不自动续费。

 默认值：`true`。 |
|worker\_auto\_renew\_period|Long|否|1|Worker节点自动续费周期，当选择包年包月付费类型时才生效，且为必选值。

 取值范围：\{1, 2, 3, 6, 12\}。 |
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
|deletion\_protection|Boolean|否|true|集群删除保护，防止通过控制台或API误删除集群。取值：

 -   `true`：启用集群删除保护，将不能通过控制台或API删除集群。
-   `false`：不启用集群删除保护，则能通过控制台或API删除集群。

 默认值：`false`。 |
|disable\_rollback|Boolean|否|true|集群创建失败是否回滚。取值：

 -   `true`：当集群创建失败时，进行回滚操作。
-   `false`：当集群创建失败时，不进行回滚操作。

 默认值：`true`。 |
|timeout\_mins|Long|否|60|集群创建超时时间，单位分钟。

 默认值：`60`。 |

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

## 创建ACK专有版集群示例

请求示例

```
POST /clusters
{
    "cluster_type":"Kubernetes",
    "name":"ACK专有版",
    "region_id":"cn-zhangjiakou",
    "disable_rollback":true,
    "timeout_mins":60,
    "kubernetes_version":"1.18.8-aliyun.1",
    "snat_entry":true,
    "endpoint_public_access":true,
    "ssh_flags":true,               // 是否允许SSH访问Kubernetes集群，即登录集群的Master节点。
    "cloud_monitor_flags":true,
    "deletion_protection":true,
    "node_cidr_mask":"26",
    "user_ca":"-----BEGIN CERTIFICATE-----\n***", // 用户自定义集群CA。
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
    "platform":"AliyunLinux",
    "user_data":"IyEvdXNyL2Jpbi9iYXNoCmVjaG8gIkhlbGxvIEFDSyEi",
    "node_port_range":"30000-32767",      // 指定节点服务端口范围，取值：30000～65535。
    "login_password":"Hello1234",
    "key_pair": "sin-name",
    "master_instance_charge_type":"PrePaid", // Master节点付费类型。
    "worker_instance_charge_type":"PrePaid",
    "master_period":1,                       // Master节点包年包月时长。
    "worker_period":1,
    "master_period_unit":"Month",            // Master节点付费周期。
    "worker_period_unit":"Month",
    "master_auto_renew":true,                // Master节点到期是否自动续费。
    "master_auto_renew_period":1,            // Master节点自动续费时长。
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
    "master_count":3,                 // Master节点数量，支持：3、5
    "master_vswitch_ids":[            // Master节点虚拟交换机列表。
        "vsw-8vbmoffowsztjaawjtyzo",
        "vsw-8vbmoffowsztjaawjtyzo",
        "vsw-8vbmoffowsztjaawjtyzo"
    ],
    "master_instance_types":[         // Master节点的实例规格。
        "ecs.c6.large",
        "ecs.c6.large",
        "ecs.c6.large"
    ],
    "master_system_disk_category":"cloud_essd",   // Master节点系统盘类型。
    "master_system_disk_size":120,                // Master节点系统盘大小，取值范围：40～500。
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

