# 创建安全沙箱Kubernetes集群

调用CreateCluster创建一个新的安全沙箱容器Kubernetes集群。

## 请求信息

请求行RequestLine

```
POST /clusters HTTP/1.1
```

特有请求头RequestHead

无，请参见[公共请求和返回结果](/cn.zh-CN/API参考/公共请求和返回结果.md)。

请求体RequestBody

```
{
  "name": "集群名称",
  "cluster_type": "集群类型: ManagedKubernetes",
  "disable_rollback": "true",
  "timeout_mins": "60",
  "region_id": "地域",
  "snat_entry": "是否配置SNATEntry。",
  "cloud_monitor_flags": "是否安装云监控插件。",
  "public_slb": "是否公网暴露集群endpoint。",
  "worker_instance_type": "worker实例规格",
  "num_of_nodes": "worker数量",
  "vpcid": "vpc id",
  "vswitch_ids": "worker节点虚拟交换机ID",
  "service_cidr": "服务CIDR",
  "login_password": "节点SSH登录密码，和key_pair二选一。",
  "key_pair":"keypair名称，和login_password二选一。",
  "worker_system_disk_category": "Worker系统盘类型",
  "worker_system_disk_size": "Worker节点系统盘大小",
  "worker_data_disk_category": "Worker节点数据盘类型",
  "worker_data_disk_size": "Worker节点数据盘大小",
  "worker_data_disk": "是否挂载数据盘true|false。",
  "worker_instance_charge_type":"Worker节点付费类型PrePaid|PostPaid",
  "worker_period_unit":"包年包月单位，Month,Year，只有在PrePaid下生效。",
  "worker_period":"包年包月时长，只有在PrePaid下生效。",
  "worker_auto_renew":"Worker节点自动续费true|false",
  "worker_auto_renew_period":"Worker节点续费周期",
  "kubernetes_version": "kubernetes版本",
  "addons": [{"name":"terway-eniip"}],
  "runtime": {"name": "Sandboxed-Container.runv", "version":"1.0.0"},
  "pod_vswitch_ids": "Pod虚拟交换机id数组，每一个节点虚拟交换机指定至少一个相同可用区的 Pod 虚拟交换机。"
}
```

|名称|类型|必须|描述|
|--|--|--|--|
|addons|list|是|Kubernetes集群的addon插件的组合。 -   addons的参数：
    -   name：必填，addon插件的名称。
    -   version：可选，取值为空时默认取最新版本。
    -   config：可选，取值为空时表示无需配置。
-   日志服务：可选。
    -   如果不开启日志服务时，将无法使用集群审计功能。
    -   如果开启日志服务时，在addon数组中添加`{ "name":"aliyun-log-controller"}`。 |
|cluster\_type|string|是|集群类型 安全沙箱容器当前支持的集群类型为ManagedKubernetes。 |
|key\_pair|string|是|keypair名称，和login\_password二选一。|
|kubernetes\_version|string|是|安全沙箱容器仅支持`1.14.6-aliyun.1`及以上版本。|
|login\_password|string|是|SSH登录密码。密码规则为8 ~ 30个字符，且同时包含三项（大小写字母、数字和特殊符号），和key\_pair二选一。|
|name|string|是|集群名称，集群名称可以使用大小写英文字母、中文、数字、中划线。|
|num\_of\_nodes|int|是|Worker节点数。范围是\[0，100\]。|
|pod\_vswitch\_ids|list|是|Pod使用的虚拟交换机Id列表。当网络插件使用terway-eniip时，才需配置该参数。|
|region\_id|string|是|集群所在地域ID。|
|runtime|json|是|-   name：安全容器运行时的名称，目前支持Sandboxed-Container.runv。
-   version：例如v1.0.0，当前最新版本可以通过控制台创建集群查看。 |
|snat\_entry|bool|是|是否为网络配置SNAT。 -   当已有VPC能访问公网环境时，设置为false。
-   当已有VPC不能访问公网环境时：
    -   设置为true，表示配置SNAT，此时可以访问公网环境。
    -   设置为false，表示不配置SNAT，此时不能访问公网环境。 |
|vpcid|string|是|VPC ID。如果不设置，系统会自动创建VPC，系统创建的VPC网段为192.168.0.0/16。 **说明：** vpcid和vswitch\_ids只能同时为空或者同时都设置对应的值。 |
|vswitch\_ids|list|是|交换机ID。List长度范围为\[1, 3\]。|
|worker\_data\_disk|bool|是|是否挂载数据盘，在安全沙箱容器场景下，必须选择true。|
|worker\_data\_disk\_size|string|是|数据盘大小，单位为GiB。安全沙箱容器场景下，必须大于200。|
|worker\_instance\_type|string|是|安全沙箱容器实例规格。 支持以下类型的实例规格：-   ecs.ebmg5s.24xlarge
-   ecs.ebmc5s.24xlarge
-   ecs.ebmgn6i.24xlarge
-   ecs.ebmc6.26xlarge
-   ecs.ebmg6.26xlarge
-   ecs.ebmr6.26xlarge
-   ecs.ebmgn6i.24xlarge
-   ecs.ebmgn6v.24xlarge
-   ecs.ebmhfg6.20xlarge
-   ecs.ebmhfr6.20xlarge |
|worker\_system\_disk\_category|string|是|Worker节点系统盘类型。|
|worker\_system\_disk\_size|int|是|Worker节点系统盘大小，单位为GiB。|
|cloud\_monitor\_flags|bool|否|是否安装云监控插件： -   true：表示安装。
-   false：表示不安装。 |
|disable\_rollback|bool|否|失败是否回滚： -   true：表示失败不回滚。
-   false：表示失败回滚。

默认为true，如果选择失败回滚，则会释放创建过程中所生产的资源，不推荐使用false。|
|endpoint\_public\_access|bool|否|是否开启公网API Server： -   true：默认为True，表示开放公网API Server。
-   false：若设置为false， 则不会创建公网的API Server，仅创建私网的API Server。 |
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
|timeout\_mins|int|否|集群资源栈创建超时时间，以分钟为单位，默认值60。|
|worker\_auto\_renew|bool|否|是否开启Worker节点自动续费，可选值为： -   true：自动续费。
-   false：不自动续费。 |
|worker\_auto\_renew\_period|int|否|自动续费周期，当选择预付费和自动续费时才生效，且为必选值： -   `PeriodUnit=Week`时，取值：\{“1”，“2”， “3”\}。
-   `PeriodUnit=Month`时，取值\{“1”， “2”， “3”， “6”， “12”\}。 |
|worker\_data\_disk\_category|string|否|数据盘类型，只在挂载Worker节点数据盘时有效，取值范围： -   cloud：普通云盘。
-   cloud\_efficiency：高效云盘。
-   cloud\_ssd：SSD云盘。 |
|worker\_instance\_charge\_type|string|否|Worker节点付费类型，可选值为： -   PrePaid：预付费。
-   PostPaid：按量付费。

默认为按量付费。|
|worker\_period|int|否|包年包月时长，当worker\_instance\_charge\_type取值为PrePaid时才生效且为必选值，取值范围： -   `PeriodUnit=Week`时，Period取值：\{“1”， “2”， “3”， “4”\}。
-   `PeriodUnit=Month`时，Period取值：\{ “1”， “2”， “3”，“4”， “5”， “6”， “7”， “8”， “9”， “12”，“24”， “36”，”48”，”60”\}。 |
|worker\_period\_unit|string|否|当指定为PrePaid的时候需要指定周期。可选择为： -   Week：以周为计时单位。
-   Month：以月为计时单位。 |

## 返回信息

返回行ResponseLine

```
HTTP/1.1 202 Accepted
```

特有返回头ResponseHead

无，请参见[公共请求和返回结果](/cn.zh-CN/API参考/公共请求和返回结果.md)。

|名称|类型|描述|
|--|--|--|
|cluster\_id|string|集群实例ID。|
|request\_id|string|请求ID。|
|task\_id|string|任务ID。系统自动分配，用户查询任务状态。|

## 示例

请求示例

```
POST /clusters HTTP/1.1
<公共请求头>
{
  "name": "test-sandbox", 
  "cluster_type": "ManagedKubernetes",
  "disable_rollback": true,
  "timeout_mins": 60,
  "region_id": "cn-hangzhou",
  "snat_entry": true,
  "cloud_monitor_flags": false,
  "endpoint_public_access": true,
  "worker_instance_type": "ecs.ebmg5s.24xlarge",
  "num_of_nodes": 2,
  "vpcid": "vpc-bp1iybm49v9jgb50xxxxx",
  "vswitch_ids": ["vsw-bp1ue9z93i9zpcblxxxxx"],
  "service_cidr": "192.168.0.0/16",
  "login_password": "xxxxxxxx",
  "worker_system_disk_category": "cloud_efficiency",
  "worker_system_disk_size": 200,
  "worker_data_disk_category": "cloud_efficiency",
  "worker_data_disk_size": 200,
  "worker_data_disk": true,
  "worker_instance_charge_type": "PostPaid",
  "kubernetes_version": "1.14.6-aliyun.1",
  "addons": [{"name":"terway-eniip"}],
  "runtime": {"name": "Sandboxed-Container.runv", "version": "1.0.0"},
  "pod_vswitch_ids":["vsw-bp18nj6xxfoc2ci2xxxxx"]
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

