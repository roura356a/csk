# 创建Kubernetes专有版集群

调用CreateCluster创建一个标准Kubernetes专有版集群实例，并新建指定数量的节点。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=CS&api=CreateCluster&type=ROA&version=2015-12-15)

## 请求头

该接口使用公共请求头，无特殊请求头。请参见公共请求参数文档。

## 请求语法

```
POST /clusters 
```

## 请求参数

|名称|类型|位置|是否必选|示例值|描述|
|--|--|--|----|---|--|
| |Object|Body|否| |请求体数据。 |
|addons|Array|Body|否| |Kubernetes集群的安装的组件列表：

 -   addons的参数：
    -   name：必填，组件名称。
    -   config：可选，取值为空时表示无需配置。
    -   disabled：可选，是否禁止默认安装。
-   网络插件：必选，包含Flannel和Terway网络两种网络类型，创建集群时二选一：
    -   `flannel`网络：\[\{"name":"flannel","config":""\}\]
    -   `terway`网络：\[\{"name": "terway-eniip","config": ""\}\]
-   存储插件：必选，支持`csi`和`flexvolume`两种类型：
    -   `csi`插件：\[\{"name":"csi-plugin","config": ""\},\{"name": "csi-provisioner","config": ""\}\]
    -   `flexvolume`插件：\[\{"name": "flexvolume","config": ""\}\]
-   日志组件：可选，如果不开启日志服务时，将无法使用集群审计功能：
    -   使用已有`sls project`：\[\{"name": "logtail-ds","config": "\{"IngressDashboardEnabled":"true","sls\_project\_name":"your\_sls\_project\_name"\}"\}\]
    -   创建新的`sls project`：\[\{"name": "logtail-ds","config": "\{"IngressDashboardEnabled":"true"\}"\}\]
-   Ingress：可选，默认开启安装Ingress组件nginx-ingress-controller：
    -   安装`Ingress`并且开启公网：\[\{"name":"nginx-ingress-controller","config":"\{"IngressSlbNetworkType":"internet"\}"\}\]
    -   不安装`Ingress`：\[\{"name": "nginx-ingress-controller","config": "","disabled": true\}\]
-   事件中心：可选，默认开启。事件中心提供对Kubernetes事件的存储、查询、告警等能力。默认可免费存储90天内的事件，请参见[查看详情](~~150476~~)。开启事件中心：\[\{"name":"ack-node-problem-detector","config":"\{"sls\_project\_name":""\}"\}\] |
|config|String|Body|否|\{\\"IngressDashboardEnabled\\":\\"true\\"\}|Addon配置。 |
|name|String|Body|否|logtail-ds|Addon名称。 |
|cloud\_monitor\_flags|Boolean|Body|否|true|集群是否安装云监控插件。 |
|cluster\_type|String|Body|否|Kubernetes|集群类型。

 -   Kubernetes: 专有版集群。
-   ManagedKubernetes：托管版集群。
-   Ask：Serverless 集群。
-   ExternalKubernetes：注册集群。
-   ServiceMesh：ASM 集群。 |
|container\_cidr|String|Body|否|172.20.0.0/16|POD网络地址段，不能和VPC网段冲突。当选择系统自动创建VPC时，默认使用172.16.0.0/16网段。当创建flannel网络类型的集群时，该字段为必填。 |
|cpu\_policy|String|Body|否|none|节点CPU管理策略。集群版本为1.12.6及以上版本支持static和none两种策略。默认为none。 |
|deletion\_protection|Boolean|Body|否|true|集群是否开启集群删除保护，防止通过控制台或API误删除集群。 |
|disable\_rollback|Boolean|Body|否|true|集群创建失败是否回滚。 |
|endpoint\_public\_access|Boolean|Body|否|true|是否开启公网API Server：

 -   true：默认为True，表示开放公网API Server。
-   false：若设置为false， 则不会创建公网的API Server，仅创建私网的API Server。 |
|format\_disk|Boolean|Body|否|false|使用已有实例创建集群时，是否对实例进行数据盘挂载，将容器和镜像存储在数据盘。 |
|image\_id|String|Body|否|m-bp16z7xko3vvv8gt\*\*\*\*|镜像ID。 |
|instances|Array of String|Body|否|i-2ze4zxnm36vq00xn\*\*\*\*|实例名称。 |
|is\_enterprise\_security\_group|Boolean|Body|否|true|自动创建企业级安全组，当security\_group\_id为空的时生效。 |
|keep\_instance\_name|Boolean|Body|否|true|使用已有实例创建集群时，是否保留实例名称，默认保留。 |
|key\_pair|String|Body|否|secrity-key|密钥对名称，和`login_password`二选一。 |
|kubernetes\_version|String|Body|否|1.16.9-aliyun.1|Kubernetes集群版本，默认最新版。 |
|login\_password|String|Body|否|Hello@1234|SSH登录密码。密码规则为8~30 个字符，且至少同时包含三项（大小写字母、数字和特殊符号），和`key_pair`二选一。 |
|master\_auto\_renew|Boolean|Body|否|true|Master节点是否开启自动续费，当`master_instance_charge_type`取值为`PrePaid`时才生效，可选值为：

 -   true：自动续费。
-   false：不自动续费。 |
|master\_auto\_renew\_period|Long|Body|否|1|Master节点自动续费周期，当选择预付费和自动续费时才生效，且为必选值： `PeriodUnit=Month`时，取值\{“1”， “2”， “3”， “6”， “12”\}。 |
|master\_count|Long|Body|否|3|Master节点数量，可选值3或5。默认值为3。 |
|master\_instance\_charge\_type|String|Body|否|PrePaid|Master节点付费类型，可选值为：

 -   PrePaid：预付费
-   PostPaid：按量付费

 默认为按量付费。 |
|master\_instance\_types|Array of String|Body|否|ecs.n4.xlarge|Master节点实例类型。 |
|master\_period|Long|Body|否|1|Master节点包年包月时长，当master\_instance\_charge\_type取值为PrePaid时才生效且为必选值，取值范围： PeriodUnit=Month时，Period取值：\{ “1”， “2”， “3”，“6”，“12”\}。 |
|master\_period\_unit|String|Body|否|Month|Master节点付费周期，当指定为PrePaid的时候需要指定周期。Month：以月为计时单位。 |
|master\_system\_disk\_category|String|Body|否|cloud\_ssd|Master节点系统盘类型，取值：

 -   cloud\_efficiency：高效云盘。
-   cloud\_ssd：SSD云盘。 |
|master\_system\_disk\_size|Long|Body|否|120|Master节点系统盘类型，单位GB。 |
|master\_vswitch\_ids|Array of String|Body|否|vsw-2ze3ds0mdip0hdz8i\*\*\*\*|虚拟交换ID。 |
|name|String|Body|否|cluster-demo|集群名称， 集群名称可以使用大小写英文字母、中文、数字、中划线。 |
|node\_cidr\_mask|String|Body|否|25|节点IP数量，通过指定网络的CIDR来确定IP的数量。 |
|node\_port\_range|String|Body|否|30000~32767|节点服务端口。取值范围为\[30000，65535\]。 |
|num\_of\_nodes|Long|Body|否|3|Worker节点数。范围是\[0，100\]。 |
|os\_type|String|Body|否|Linux|操作系统平台类型。取值范围：

 -   Windows
-   Linux（默认） |
|platform|String|Body|否|CentOS|操作系统发行版。取值范围：

 -   CentOS
-   AliyunLinux
-   Windows
-   WindowsCore |
|pod\_vswitch\_ids|Array of String|Body|否|vsw-2ze97jwri7cei0mpw\*\*\*\*|虚拟交换ID。 |
|private\_zone|Boolean|Body|否|false|是否开启PrivateZone用于服务发现，取值为true或者false。请参见[Serverless集群基于云解析PrivateZone的服务发现](~~86403~~)。 |
|profile|String|Body|否|Default|边缘集群标识，当创建集群类型为边缘托管版时，该参数必填。

 -   Default：非边缘集群。
-   Edge：边缘集群。 |
|proxy\_mode|String|Body|否|ipvs|kube-proxy代理模式，支持iptables和ipvs两种模式，默认为ipvs 。 |
|rds\_instances|Array of String|Body|否|rm-2zev748xi27xc\*\*\*\*|RDS实例名称。 |
|region\_id|String|Body|否|cn-beijing|集群所在的地域ID。 |
|runtime|Object|Body|否| |集群内容器运行时。 |
|name|String|Body|否|docker|容器运行时名称。 |
|version|String|Body|否|19.03.5|容器运行时版本。 |
|security\_group\_id|String|Body|否|""|使用已有安全组ID。 |
|service\_cidr|String|Body|否|172.21.0.0/20|Service网络的地址段，不能和VPC网段及Pod网络网段冲突。当选择系统自动创建VPC时，默认使用172.19.0.0/20网段。 |
|snat\_entry|Boolean|Body|否|true|是否为网络配置SNAT：

 -   当已有VPC能访问公网环境时，设置为 false。
-   当已有VPC不能访问公网环境时：
    -   设置为true，表示配置SNAT，此时可以访问公网环境。
    -   设置为false，表示不配置SNAT，此时不能访问公网环境。 |
|ssh\_flags|Boolean|Body|否|true|是否开放公网SSH登录：

 -   true：表示开放。
-   false：表示不开放。 |
|tags|Array|Body|否| |集群标签。 |
|key|String|Body|否|env|标签key。 |
|value|String|Body|否|prod|标签值。 |
|taints|Array|Body|否| |节点污点信息。 |
|effect|String|Body|否|NoSchedule|调度策略。 |
|key|String|Body|否|disk\_type|污点key。 |
|value|String|Body|否|ssd|污点值。 |
|timeout\_mins|Long|Body|否|60|集群创建超时时间。 |
|user\_data|String|Body|否|""|节点自定义数据，Windows 支持 bat 和 powershell 两种格式，在 Base64 编码前，第一行为 \[bat\] 或者 \[powershell\]。Linux 支持 shell 脚本，更多的格式请参见 [cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/format.html) \| [查看详情](~~49121~~)。

 如果您使用的自定义脚本大小大于 1 KB，建议您将脚本上传到 OSS，通过 OSS 内网端点拉取脚本执行。

 创建集群或添加节点提交成功不代表实例自定义脚本执行成功，请自行确定脚本执行情况。 |
|vpcid|String|Body|否|vpc-2zeik9h3ahvv2zz95\*\*\*\*|集群使用的VPC。 |
|vswitch\_ids|Array of String|Body|否|vsw-2ze48rkq464rsdts1\*\*\*\*"|虚拟交换ID。 |
|worker\_auto\_renew|Boolean|Body|否|true|Worker节点是否开启自动续费，可选值为：

 -   true：自动续费。
-   false：不自动续费。 |
|worker\_auto\_renew\_period|Long|Body|否|1|Worker节点自动续费周期，当选择预付费和自动续费时才生效，且为必选值： `PeriodUnit=Month`时，取值\{“1”， “2”， “3”， “6”， “12”\}。 |
|worker\_data\_disks|Array|Body|否| |Worker节点数据盘配置。 |
|auto\_snapshot\_policy\_id|String|Body|否|""|是否开启云盘备份。 |
|category|String|Body|否|cloud\_ssd|数据盘类型。 |
|encrypted|String|Body|否|false|是否对数据盘加密。 |
|size|String|Body|否|40|数据盘大小。 |
|worker\_instance\_charge\_type|String|Body|否|PrePaid|Worker节点付费类型，可选值为：

 -   PrePaid：预付费
-   PostPaid：按量付费

 默认为按量付费。 |
|worker\_instance\_types|Array of String|Body|否|ecs.n4.large|Worker节点实例类型。 |
|worker\_period|Long|Body|否|1|Worker节点包年包月时长，当`worker_instance_charge_type`取值为`PrePaid`时才生效且为必选值，取值范围： `PeriodUnit=Month`时，Period取值：\{ “1”， “2”， “3”，“6”，“12”\}。 |
|worker\_period\_unit|String|Body|否|Month|Wroker节点付费周期，当指定为PrePaid的时候需要指定周期。Month：以月为计时单位。 |
|worker\_system\_disk\_category|String|Body|否|cloud\_efficiency|Worker节点系统盘类型，取值：

 -   cloud\_efficiency：高效云盘。
-   cloud\_ssd：SSD云盘。 |
|worker\_system\_disk\_size|Long|Body|否|120|Worker节点系统盘大小，单位GB。 |
|worker\_vswitch\_ids|Array of String|Body|否|vsw-2ze3ds0mdip0hdz8i\*\*\*\*|虚拟交换机ID。 |
|zone\_id|String|Body|否|""|集群所在的可用区ID。 |

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
公共请求头
{
    "cluster_type":"Kubernetes",
    "name":"webService",
    "region_id":"cn-beijing",
    "disable_rollback":true,
    "timeout_mins":60,
    "kubernetes_version":"1.14.8-aliyun.1",
    "snat_entry":true,
    "endpoint_public_access":true,
    "ssh_flags":true,
    "cloud_monitor_flags":true,
    "deletion_protection":false,
    "node_cidr_mask":"26",
    "proxy_mode":"ipvs",
    "tags":[],
    "addons":[{"name":"flannel"},{"name":"arms-prometheus"},{"name":"flexvolume"},{"name":"alicloud-disk-controller"},{"name":"logtail-ds","config":"{"IngressDashboardEnabled":"false"}"},{"name":"ack-node-problem-detector","config":"{"sls_project_name":""}"},{"name":"nginx-ingress-controller","config":"{"IngressSlbNetworkType":"internet"}"}],
    "os_type":"Linux",
    "platform":"CentOS",
    "node_port_range":"30000-32767",
    "key_pair":"sian-sshkey",
    "cpu_policy":"none",
    "master_count":3,
    "master_vswitch_ids":["vsw-2zete8s4qocqg0mf6****","vsw-2zete8s4qocqg0mf6****","vsw-2zete8s4qocqg0mf6****"],
    "master_instance_types":["ecs.n4.large","ecs.n4.large","ecs.n4.large"],
    "master_system_disk_category":"cloud_ssd",
    "master_system_disk_size":120,
    "runtime":{"name":"docker","version":"18.09.2"},
    "worker_instance_types":["ecs.i1.xlarge"],
    "num_of_nodes":1,
    "worker_system_disk_category":"cloud_efficiency",
    "worker_system_disk_size":120,
    "vpcid":"vpc-2zecuu62b9zw7a7q****",
    "worker_vswitch_ids":["vsw-2zete8s4qocqg0mf6****"],
    "container_cidr":"172.20.0.0/16",
    "service_cidr":"172.21.0.0/20"
}
```

请求示例补充说明

```
如果是terway网络类型的集群，"pod_vswitch_ids"为必填参数，请求入参示例如下：

POST /clusters HTTP/1.1
公共请求头
{
	"cluster_type":"Kubernetes",
	"name":"webService-terway",
	"region_id":"cn-beijing",
	"disable_rollback":true,
	"timeout_mins":60,
	"kubernetes_version":"1.14.8-aliyun.1",
	"snat_entry":true,
	"endpoint_public_access":true,
	"ssh_flags":true,"cloud_monitor_flags":true,
	"deletion_protection":false,
	"proxy_mode":"ipvs",
	"tags":[],
	"addons":[{"name":"terway-eni"},{"name":"flexvolume"},{"name":"alicloud-disk-controller"},{"name":"logtail-ds","config":"{\"IngressDashboardEnabled\":\"false\"}"},{"name":"ack-node-problem-detector","config":"{\"sls_project_name\":\"\"}"},{"name":"nginx-ingress-controller","config":"{\"IngressSlbNetworkType\":\"internet\"}"}],
	"os_type":"Linux",
	"platform":"CentOS",
	"node_port_range":"30000-32767",
	"pod_vswitch_ids":["vsw-2zete8s4qocqg0mf6****"],
	"key_pair":"sian-sshkey",
	"cpu_policy":"none",
	"master_count":3,
	"master_vswitch_ids":["vsw-2zed90q9inwtuyfzd****","vsw-2zed90q9inwtuyfzd****","vsw-2zed90q9inwtuyfzd****"],
	"master_instance_types":["ecs.i1.4xlarge","ecs.i1.4xlarge","ecs.i1.4xlarge"],
	"master_system_disk_category":"cloud_ssd",
	"master_system_disk_size":120,
	"runtime":{"name":"docker","version":"18.09.2"},
	"worker_instance_types":["ecs.i1.4xlarge"],
	"num_of_nodes":1,
	"worker_system_disk_category":"cloud_efficiency",
	"worker_system_disk_size":120,
	"vpcid":"vpc-2zecuu62b9zw7a7qnn2tv",
	"worker_vswitch_ids":["vsw-2zed90q9inwtuyfzd****"],
	"is_enterprise_security_group":true,
	"service_cidr":"172.21.0.0/20"
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

