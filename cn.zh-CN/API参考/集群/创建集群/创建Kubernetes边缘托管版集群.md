# 创建Kubernetes边缘托管版集群

调用CreateCluster创建一个新的Kubernetes边缘托管版集群实例。

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
"disable_rollback": "失败是否回滚。",
"name": "集群名称。",
"timeout_mins": "集群创建超时时间。",
"cluster_type": "集群类型，ManagedKubernetes。",
"profile":"边缘集群标志，Edge。",
"region_id": "地域",
"vpcid": "VPC ID",
"vswitch_ids": "一台或多台虚拟交换机ID，N的取值范围为[1, 5]。",
"container_cidr": "容器POD CIDR。",
"service_cidr": "服务CIDR",
"cloud_monitor_flags":"是否安装云监控插件。",
"login_password": "节点SSH登录密码，和key_pair二选一。",
"key_pair":"keypair名称，和login_password二选一。",
"worker_instance_types": "Worker实例规格多实例规格参数。 ",
"worker_system_disk_category": "Worker系统盘类型。",
"worker_system_disk_size": "Worker节点系统盘大小。",
"worker_data_disk":"是否挂载数据盘true|false",
"worker_data_disk_category":"数据盘类型。",
"worker_data_disk_size":"数据盘大小",
"num_of_nodes": "Worker节点数",
"snat_entry": "是否配置SNATEntry。",
"endpoint_public_access": "是否公网暴露集群endpoint。",
"tags": "给集群打tag标签, 数组格式对象。"
}
```

|名称|类型|必须|描述|
|--|--|--|--|
|cluster\_type|string|是|集群类型|
|key\_pair|string|是|keypair名称，与login\_password二选一。|
|login\_password|string|是|SSH登录密码。密码规则为8 - 30个字符，且同时包含三项（大、小写字母，数字和特殊符号），和key\_pair二选一。|
|name|string|是|集群名称，集群名称可以使用大小写英文字母、中文、数字、中划线。|
|num\_of\_nodes|int|是|Worker节点数。范围是\[0，300\]。|
|profile|string|是|边缘集群标识，默认取值为Edge。|
|region\_id|string|是|集群所在地域ID。|
|snat\_entry|bool|是|是否为网络配置SNAT。如果是自动创建VPC必须设置为true。如果使用已有VPC则根据是否具备出网能力来设置。|
|vswitch\_ids|list|是|交换机ID。List长度范围为 \[1，3\]。|
|worker\_system\_disk\_category|string|是|Worker节点系统盘类型。|
|worker\_system\_disk\_size|int|是|Worker节点系统盘大小。|
|container\_cidr|string|否|容器网段，不能和VPC网段冲突。当选择系统自动创建VPC时，默认使用172.16.0.0/16网段。|
|cloud\_monitor\_flags|bool|否|是否安装云监控插件。 **说明：** 如果您选择了使用日志或者监控服务，会自动购买一个云端ECS节点用于部署相应管控服务，那么需要配置云端worker的登录信息和资源规格信息。 |
|disable\_rollback|bool|否|失败是否回滚： -   true：表示失败不回滚。
-   false：表示失败回滚。

如果选择失败回滚，则会释放创建过程中所生产的资源，不推荐使用false。|
|proxy\_mode|string|否|kube-proxy代理模式，支持iptables和IPVS两种模式。 默认为iptables。|
|endpoint\_public\_access|bool|否|是否开启公网API Server： -   true：默认为True，表示开放公网API Server。
-   false：若设置为false， 则不会创建公网的API Server，仅创建私网的API Server。

**说明：** 在边缘集群场景，边缘节点通过公网和云端管控交互；因此，边缘集群需要开启公网访问。 |
|service\_cidr|string|否|服务网段，不能和VPC网段以及容器网段冲突。当选择系统自动创建VPC时，默认使用172.19.0.0/20网段。|
|addons|Array|否|Kubernetes集群的安装的组件列表：

-   addons的参数：
    -   name：必填，组件名称。
    -   config：可选，取值为空时表示无需配置。
    -   disabled：可选，是否禁止默认安装。
-   网络插件：必选，目前只支持`flannel`。网络：\[\{"name":"flannel","config":""\}\]
-   日志组件：可选，如果不开启日志服务时，将无法使用集群审计功能：
    -   使用已有`sls project`\[\{"name":"logtail-ds-docker"\},\{"name":"alibaba-log-controller","config":"\{"IngressDashboardEnabled":"false","sls\_project\_name":"your\_sls\_project\_name"\}"\}\]
    -   创建新的`sls project`：\[\{"name":"logtail-ds-docker","config":""\},\{"name":"alibaba-log-controller","config":"\{"IngressDashboardEnabled":"false"\}"\}\] |
|tags|list|否|给集群打tag标签： -   key：标签名称
-   value：标签值 |
|timeout\_mins|int|否|集群资源栈创建超时时间，以分钟为单位，默认值60分钟。|
|vpcid|string|否|VPC ID，可空。如果不设置，系统会自动创建VPC，系统创建的VPC网段为192.168.0.0/16。VpcId和vswitchid只能同时为空或者同时都设置相应的值。|
|worker\_data\_disk|string|否|是否挂载数据盘，可选择： -   true：表示worker节点挂载数据盘。
-   false：表示worker节点不挂载数据盘。 |
|worker\_data\_disk\_category|int|否|数据盘类型|
|worker\_data\_disk\_size|string|否|数据盘大小|

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

返回示例

```
HTTP/1.1 202 Accepted
<公共响应头>
{
    "cluster_id": "cb95aa626a47740afbf6aa099b65****",
    "RequestId": "687C5BAA-D103-4993-884B-C35E4314****",
    "task_id": "T-5a54309c80282e39ea00****"
}
```

