# 创建多可用区 Kubernetes 集群 {#reference_yz2_qnk_xdb .reference}

创建一个新的跨可用区的高可用的Kubernetes集群实例，并新建指定数量的节点。

## 请求信息 {#section_gm1_wnk_xdb .section}

**请求行 RequestLine**

```
POST /clusters HTTP/1.1
```

**特有请求头 RequestHead**

无，请参考[公共请求头部](intl.zh-CN/开发指南/Kubernetes API 参考/集群 API 调用方式/公共参数.md#section_mr5_lf1_wdb)。

**请求体 RequestBody**

```
{
    "disable_rollback":"失败是否回滚",
    "name": "集群名称",
    "timeout_mins": 集群创建超时时间,
    "cluster_type": "Kubernetes",
    "region_id": "地域"
    "multi_az": true,
    "vpcid": "VPC ID ",
    "container_cidr": "容器 CIDR",         
    "service_cidr": "服务 CIDR",
    "vswitch_id_a": "第一个可用区交换机ID",
    "vswitch_id_b": "第二个可用区交换机ID",
    "vswitch_id_c": "第三个可用区交换机ID",        
    "master_instance_type_a": "第一个可用区Master节点实例规格",
    "master_instance_type_b": "第二个可用区Master节点实例规格",
    "master_instance_type_c": "第三个可用区Master节点实例规格", 
    "master_system_disk_category": "Master节点系统盘类型",
    "master_system_disk_size": "Master节点系统盘大小",               
    "worker_instance_type_a": "第一个可用区Worker节点实例规格",
    "worker_instance_type_b": "第二个可用区Worker节点实例规格",
    "worker_instance_type_c": "第三个可用区Worker节点实例规格",
    "worker_system_disk_category": "Worker节点系统盘类型",
    "worker_system_disk_size": "Worker节点系统盘大小",
    "num_of_nodes_a": "第一个可用区Worker节点数",
    "num_of_nodes_b": "第二个可用区Worker节点数",
    "num_of_nodes_c": "第三个可用区Worker节点数",
    "ssh_flags": "是否开放公网SSH登录",
    "login_password": "SSH登录密码",             "cloud_monitor_flags":"是否安装云监控插件"
}
```

**请求体解释**

|名称|类型|必须|描述|
|:-|:-|:-|:-|
|disable\_rollback|bool|否|失败是否回滚，true 表示失败不回滚，false 失败回滚。如果选择失败回滚，则会释放创建过程中所生产的资源，不推荐使用 false|
|name|string|是|集群名称, 集群名称可以使用大小写英文字母、中文、数字、中划线。|
|timeout\_mins|int|是|集群资源栈创建超时时间,以分钟为单位，默认值 60。|
|region\_id|string|是|集群所在地域ID|
|multi\_az|bool|是|高可用集群类型，固定为 true|
|vpcid|string|是|VPCID|
|container\_cidr|string|否|容器网段, 不能和VPC网段冲突。当选择系统自动创建VPC时，默认使用172.16.0.0/16网段|
|service\_cidr|string|否|服务网段，不能和VPC网段以及容器网段冲突。当选择系统自动创建VPC时，默认使用172.19.0.0/20网段|
|vswitch\_id\_a|string|是|第一个可用区的交换机ID|
|vswitch\_id\_b|string|是|第二个可用区的交换机ID|
|vswitch\_id\_c|string|是|第三个可用区的交换机ID|
|master\_instance\_type\_a|string|是|第一个可用区Master节点 ECS 规格类型代码。更多详细信息，参见[实例规格族](../../../../intl.zh-CN/产品简介/实例规格族.md#)。|
|master\_instance\_type\_b|string|是|第二个可用区Master节点 ECS 规格类型代码。更多详细信息，参见[实例规格族](../../../../intl.zh-CN/产品简介/实例规格族.md#)。|
|master\_instance\_type\_c|string|是|第三个可用区Master节点 ECS 规格类型代码。更多详细信息，参见[实例规格族](../../../../intl.zh-CN/产品简介/实例规格族.md#)。|
|master\_system\_disk\_category|string|是|Master节点系统盘类型。|
|master\_system\_disk\_size|int|是|Master节点系统盘大小|
|worker\_instance\_type\_a|string|是|第一个可用区Worker 节点 ECS 规格类型代码。更多详细信息，参见[实例规格族](../../../../intl.zh-CN/产品简介/实例规格族.md#)。|
|worker\_instance\_type\_b|string|是|第一个可用区Worker 节点 ECS 规格类型代码。更多详细信息，参见[实例规格族](../../../../intl.zh-CN/产品简介/实例规格族.md#)。|
|worker\_instance\_type\_c|string|是|第一个可用区Worker 节点 ECS 规格类型代码。更多详细信息，参见[实例规格族](../../../../intl.zh-CN/产品简介/实例规格族.md#)。|
|worker\_system\_disk\_category|string|是|Worker节点系统盘类型。|
|worker\_system\_disk\_size|int|是|Worker节点系统盘大小|
|num\_of\_nodes\_a|int|是|第一个可用区Worker节点数。范围是\[1,300\]|
|num\_of\_nodes\_b|int|是|第一个可用区Worker节点数。范围是\[1,300\]|
|num\_of\_nodes\_c|int|是|第一个可用区Worker节点数。范围是\[1,300\]|
|login\_password|string|是|SSH登陆密码。密码规则为8 - 30 个字符，且同时包含三项（大、小写字母，数字和特殊符号）。和key\_pair 二选一|
|key\_pair|string|是|keypair名称。和login\_password二选一|
|ssh\_flags|bool|否|是否开放公网SSH登录登陆|
|cloud\_monitor\_flags|bool|否|是否安装云监控插件|

**API使用注意**

-   首先需要在统一个VPC下包含至少3个交换机，为了保证高可用建议3个交换机分布在不同的可用区。
-   被集群将要使用的交换机必须具备访问功能的能力\(可以使用nat网关配置SNAT规则或者使用ECS作为网络代理。推荐使用nat网关配置SNAT规则的方式\)

## 返回信息 {#section_zbx_3pk_xdb .section}

**返回行 ResponseLine**

```
HTTP/1.1 202 Accepted
```

**特有返回头 ResponseHead**

无，请参考[公共返回头部](intl.zh-CN/开发指南/Kubernetes API 参考/集群 API 调用方式/公共参数.md#section_zr5_lf1_wdb)。

**返回体 ResponseBody**

```
{
    "cluster_id":"string",
    "request_id":"string",
    "task_id":"string"
}
```

## 示例 {#section_gdx_lpk_xdb .section}

**请求示例**

```
POST /clusters HTTP/1.1
<公共请求头>
{
    "disable_rollback": true,
    "name": "mulit-az-cluster",
    "timeout_mins": 60,
    "cluster_type": "Kubernetes",
    "region_id": "cn-shanghai",
    "multi_az": true,
    "container_cidr": "10.4.0.0/16",
    "service_cidr": "10.3.0.0/20",
    "vpcid": "vpc-mytestvpc",
    "vswitch_id_a": "vsw-a",
    "vswitch_id_b": "vsw-b",
    "vswitch_id_c": "vsw-c",
    "master_instance_type_a": "ecs.c5.large",
    "master_instance_type_b": "ecs.d1.2xlarge",
    "master_instance_type_c": "ecs.c4.xlarge",
    "master_system_disk_category": "cloud_efficiency",
    "master_system_disk_size": 40,
    "worker_instance_type_a": "ecs.c5.large",
    "worker_instance_type_b": "ecs.d1.2xlarge",
    "worker_instance_type_c": "ecs.c4.xlarge",
    "worker_system_disk_category": "cloud_efficiency",
    "worker_system_disk_size": 40,
    "num_of_nodes_a": 2,
    "num_of_nodes_b": 2,
    "num_of_nodes_c": 2,
    "ssh_flags": true,
    "login_password": "Hello1234",
    "cloud_monitor_flags": true
}
```

**返回示例**

```
HTTP/1.1 202 Accepted
<公共响应头>
{
    "cluster_id": "cb95aa626a47740afbf6aa099b650d7ce",
    "request_id": "687C5BAA-D103-4993-884B-C35E4314A1E1",
    "task_id": "T-5a54309c80282e39ea00002f"
}
```

