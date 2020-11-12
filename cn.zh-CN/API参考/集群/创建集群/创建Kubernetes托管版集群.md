# 创建Kubernetes托管版集群

调用CreateCluster创建一个Kubernetes托管版集群实例，并创建指定数量的节点。

## 请求信息

请求行RequestLine

```
POST /clusters HTTP/1.1 
```

特有请求头RequestHead

该接口使用公共请求头，无特殊请求头。请参见[公共请求和返回结果](/cn.zh-CN/API参考/公共请求和返回结果.md)。

请求体RequestBody

**说明：** 创建集群时，请求参数需要正确组合，否则会导致集群创建失败。在容器服务控制台创建集群界面，提供了**生成集群创建OpenAPI参数**功能，可以为您提供准确的参数组合。请参见[生成OpenAPI参数](/cn.zh-CN/Kubernetes集群用户指南/集群管理/连接集群/生成OpenAPI参数.md)。

```
{
"disable_rollback": "失败是否回滚。",
"name": "集群名称。",
"timeout_mins": "集群创建超时时间。",
"cluster_type": "集群类型，ManagedKubernetes。",
"region_id": "地域。",
"vpcid": "VPC ID",
"vswitch_ids": "一台或多台虚拟交换机ID，交换机个数的取值范围为[1,3]。",
"container_cidr": "容器POD CIDR。",
"service_cidr": "服务CIDR。",
"cloud_monitor_flags":"是否安装云监控插件。",
"login_password": "节点SSH登录密码，和key_pair二选一。",
"key_pair":"keypair名称，和login_password二选一。",
"worker_instance_charge_type":"Worker节点付费类型PrePaid|PostPaid。",
"worker_period_unit":"包年包月单位，Month,Year，只有在PrePaid下生效。",
"worker_period":"包年包月时长，只有在PrePaid下生效。",
"worker_auto_renew":"Worker节点自动续费true|false。",
"worker_auto_renew_period":"Worker节点续费周期。",
"worker_instance_types": "Worker实例规格多实例规格参数。 ",
"worker_system_disk_category": "Worker系统盘类型。",
"worker_system_disk_size": "Worker节点系统盘大小。",
"worker_data_disk":"是否挂载数据盘true|false。",
"worker_data_disks":"Worker节点数据盘配置。",
"num_of_nodes": "Worker节点数。",
"snat_entry": "是否配置SNATEntry",
"endpoint_public_access":"是否公网暴露集群endpoint。",
"proxy_mode": "网络模式, 可选值iptables|ipvs。",
"addons": "选装addon, 数组格式对象", 
"tags": "给集群打tag标签, 数组格式对象。",
"security_group_id": "安全组ID，和is_enterprise_security_group二选一",
"is_enterprise_security_group": "是否自动创建安全组，security_group_id如果没设置，那么该参数必填，值为true",
"taints": "给节点添加taint, 数组格式对象。",
"cpu_policy":"cpu的策略，static|none。",
"runtime":"容器的运行时环境，默认为docker。例如："runtime":{"name":"docker","version":"19.03.5"}"。",
"platform":"运行Pod的主机的平台架构。",
"os_type":"运行Pod的主机的操作系统类型，例如：linux，Windows等。",
"node_cidr_mask":"节点网络的网络ID的位数。",
"kubernetes_version":"kubernetes集群的版本，默认最新版本。",
"deletion_protection":"是否开启集群删除保护，防止通过控制台或API误删除集群。"
}
```

|名称|类型|必须|描述|
|--|--|--|--|
|cluster\_type|string|是|集群类型。-   Kubernetes：Kubernetes专有版
-   ManagedKubernetes：标准托管版集群，边缘托管版集群
-   Ask：标准Serverless集群 |
|key\_pair|string|是|keypair名称。与login\_password二选一。|
|login\_password|string|是|SSH登录密码。密码规则为8 - 30个字符，且至少同时包含三项（大小写字母、数字和特殊符号）。和key\_pair二选一。|
|name|string|是|集群名称，集群名称可以使用大小写英文字母、中文、数字、中划线。|
|num\_of\_nodes|int|是|Worker节点数。范围是\[0，100\]。|
|region\_id|string|是|集群所在地域ID。|
|snat\_entry|bool|是|是否为网络配置SNAT。如果是自动创建VPC必须设置为true。如果使用已有VPC则根据是否具备出网能力来设置。|
|vswitch\_ids|list|是|交换机ID。List长度范围为 \[1，3\]。|
|worker\_system\_disk\_category|string|是|Worker节点系统盘类型。|
|worker\_system\_disk\_size|int|是|Worker节点系统盘大小。|
|addons|list|否|Kubernetes集群的addon插件的组合。 -   addons的参数：
    -   name：必填，addon插件的名称。
    -   version：可选，取值为空时默认取最新版本。
    -   config：可选。
-   网络插件：包含Flannel和Terway网络插件，二选一。
-   日志服务：可选，如果不开启日志服务时，将无法使用集群审计功能。
-   Ingress：默认开启安装Ingress组件nginx-ingress-controller。 |
|container\_cidr|string|否|容器网段，不能和VPC网段冲突。当选择系统自动创建VPC时，默认使用172.16.0.0/16网段。|
|cloud\_monitor\_flags|bool|否|是否安装云监控插件。|
|disable\_rollback|bool|否|失败是否回滚： -   true：表示失败不回滚。
-   false：表示失败回滚。

如果选择失败回滚，则会释放创建过程中所生产的资源，不推荐使用false。|
|public\_slb|bool|否|是否开启公网API Server： -   true：默认为True，表示开放公网API Server。
-   false：若设置为false， 则不会创建公网的API Server，仅创建私网的API Server。

**说明：** 过期参数，替代参数请参见endpoint\_public\_access。 |
|proxy\_mode|string|否|kube-proxy代理模式，支持iptables和IPVS两种模式。 默认为iptables。|
|endpoint\_public\_access|bool|否|是否开启公网API Server： -   true：默认为True，表示开放公网API Server。
-   false：若设置为false， 则不会创建公网的API Server，仅创建私网的API Server。 |
|security\_group\_id|string|否|指定集群ECS实例所属于的安全组ID，和is\_enterprise\_security\_group二选一。|
|is\_enterprise\_security\_group|bool|否|是否自动创建安全组，如果security\_group\_id未设置，那么is\_enterprise\_security\_group必填且值为true。|
|service\_cidr|string|否|服务网段，不能和VPC网段以及容器网段冲突。当选择系统自动创建VPC时，默认使用172.19.0.0/20网段。|
|addons|Array|否|Kubernetes集群的安装的组件列表：

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
-   事件中心：可选，默认开启。事件中心提供对Kubernetes事件的存储、查询、告警等能力。默认可免费存储90天内的事件，请参见[查看详情](/cn.zh-CN/应用中心（App）/K8S事件中心/创建并使用Kubernetes事件中心.md)。

开启事件中心：\[\{"name":"ack-node-problem-detector","config":"\{"sls\_project\_name":""\}"\}\] |
|tags|list|否|给集群打tag标签： -   key：标签名称。
-   value：标签值。 |
|taints|list|否|用于给节点做污点标记，通常用于Pods的调度策略。与之相对应的概念为：容忍（tolerance），若Pods上有相对应的tolerance标记，则可以容忍节点上的污点，并调度到该节点。|
|timeout\_mins|int|否|集群资源栈创建超时时间，以分钟为单位，默认值60分钟。|
|vpcid|string|是|VPC ID不能为空。 **说明：** vpcid和vswitchid只能同时都设置对应的值。 |
|worker\_auto\_renew|bool|否|是否开启Worker节点自动续费，可选值为： -   true：自动续费。
-   false：不自动续费。 |
|worker\_auto\_renew\_period|int|否|自动续费周期，当worker\_instance\_charge\_type取值为PrePaid时才生效且为必选值。PeriodUnit=Month时，取值为\{“1”，“2”，“3”，“6”，“12”\}。|
|worker\_data\_disk|string|否|是否挂载数据盘，可选择： -   true：表示worker节点挂载数据盘。
-   false：表示worker节点不挂载数据盘。 |
|worker\_data\_disks|list|否|Worker数据盘类型、大小等配置的组合。该参数只有在挂载Worker节点数据盘时有效，包含以下参数： -   category：数据盘类型。取值范围：
    -   cloud：普通云盘。
    -   cloud\_efficiency：高效云盘。
    -   cloud\_ssd：SSD云盘。
-   size：数据盘大小，单位为GiB。 |
|worker\_data\_disk\_category|int|否|数据盘类型 **说明：** 过期参数，替代参数请参见worker\_data\_disks参数中的category取值。 |
|worker\_data\_disk\_size|string|否|数据盘大小 **说明：** 过期参数，替代参数请参见worker\_data\_disks参数中的size取值。 |
|worker\_instance\_charge\_type|string|否|Worker节点付费类型，可选值为： -   PrePaid：预付费。
-   PostPaid：按量付费。 |
|worker\_period|int|否|包年包月时长，当worker\_instance\_charge\_type取值为PrePaid时才生效且为必选值。`PeriodUnit=Month`时，Period取值：\{ “1”，“2”，“3”，“6”，“12”\} 。|
|worker\_period\_unit|string|否|当指定为PrePaid的时候需要指定周期。 **说明：** Month：以月为计时单位。 |
|worker\_instance\_types|list|是|Worker节点ECS规格类型代码。更多详细信息，参见[实例规格族](/cn.zh-CN/实例/实例规格族.md)。|
|cpu\_policy|string|否|CPU策略。集群版本为1.12.6及以上版本支持static和none两种策略。默认为none。|
|runtime|json|否|容器运行时，例如docker、Sandboxed-Container.runv等，通常为docker。runtime包括下面2个信息： -   name：容器运行时名称。
-   version：容器运行时版本。 |
|platform|string|否|运行Pod的主机的平台架构，例如CentOS、AliyunLinux、Windows、WindowsCore等。|
|os\_type|string|否|运行Pod的主机的操作系统类型，例如Linux，Windows。|
|node\_cidr\_mask|string|否|节点网络的网络前缀。node\_cidr\_mask通过cidr限制一个节点上能运行容器网络的Pod数量。例如node\_cidr\_mask是24的话，对应的容器数量是256，25对应的就是128。计算方式：2^\(32-node\_cidr\_mask的值\)-1。|
|kubernetes\_version|string|否|Kubernetes集群的版本，默认最新版本。|
|deletion\_protection|bool|否|是否开启集群删除保护，防止通过控制台或API误删除集群。|

## 返回信息

返回行ResponseLine

```
HTTP/1.1 202 Accepted
```

特有返回头ResponseHead

无，请参见[公共请求和返回结果](/cn.zh-CN/API参考/公共请求和返回结果.md)。

返回体ResponseBody

```
{
"cluster_id":"string",
"request_id":"string",
"task_id":"string"
}
```

## 示例

请求示例

```
POST /clusters HTTP/1.1
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

```
HTTP/1.1 202 Accepted
<公共响应头>
{
    "cluster_id": "cb95aa626a47740afbf6aa099b65****",
    "RequestId": "687C5BAA-D103-4993-884B-C35E4314A1E1",
    "task_id": "T-5a54309c80282e39ea00002f"
}
```

