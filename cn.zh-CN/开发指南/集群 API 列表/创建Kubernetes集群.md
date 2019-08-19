# 创建Kubernetes集群 {#reference_cpd_gl2_xdb .reference}

创建一个新的 Kubernetes 集群实例，并新建指定数量的节点。

## 请求信息 {#section_dt4_pl2_xdb .section}

**请求行 RequestLine**

``` {#codeblock_v3t_v3o_f2z}
POST /clusters HTTP/1.1
```

**特有请求头 RequestHead**

无，请参考[公共请求头部](cn.zh-CN/开发指南/集群 API 调用方式/公共参数.md#section_mr5_lf1_wdb)。

**请求体 RequestBody**

``` {#codeblock_5cg_dbi_lgs}
{
    "disable_rollback": "失败是否回滚",
    "name": "集群名称",
    "timeout_mins": 集群创建超时时间,
    "cluster_type": "集群类型，Kubernetes",
    "region_id": "地域",
    "vpcid": "VPC ID",
    "master_vswitch_ids": "master节点交换机ID。填写3个vswitchid, 尽量是分别在3个不同az, 保证高可用",
    "master_instance_types": "master节点实例类型。填写3个实例规格"
    "master_count": "master实例个数，支持3|5"
    "container_cidr": "容器POD CIDR",
    "service_cidr": "服务CIDR",
    "ssh_flags": "是否开放公网SSH登录",
    "cloud_monitor_flags":"是否安装云监控插件",
    "login_password": "节点SSH登录密码，和key_pair二选一",
    "key_pair":"keypair名称，和login_password 二选一",
    "master_instance_charge_type":"Master实例付费类型，PostPaid|PrePaid",
    "master_period_unit":"包年包月单位，Month,Year，只有在PrePaid下生效",
    "master_period":"包年包月时长，只有在PrePaid下生效",
    "master_auto_renew":"Master节点是否自动续费",
    "master_auto_renew_period":"Master节点续费周期",
    "master_system_disk_category": "Master系统盘类型",
    "master_system_disk_size":"Master节点系统盘大小",
    "master_data_disk":"Master节点是否挂载数据盘",
    "master_data_disk_category":"Master节点数据盘类型",
    "master_data_disk_size":"Master节点数据盘大小",
    "worker_instance_charge_type":"Worker节点付费类型PrePaid|PostPaid",
    "worker_period_unit":"包年包月单位，Month,Year，只有在PrePaid下生效",
    "worker_period":"包年包月时长，只有在PrePaid下生效",
    "worker_auto_renew":"Worker节点自动续费true|false",
    "worker_auto_renew_period":"Worker节点续费周期",
    "worker_instance_types": "Worker实例规格多实例规格参数. ",
    "worker_vswitch_ids":  "一台或多台虚拟交换机 ID，N 的取值范围为 [1, 5]",
    "worker_system_disk_category": "Worker系统盘类型",
    "worker_system_disk_size": "Worker节点系统盘大小",
    "worker_data_disk":"Worker节点是否挂载数据盘",
    "worker_data_disk_category":"Worker节点数据盘类型",
    "worker_data_disk_size":"Worker节点数据盘大小",
    "num_of_nodes": "Worker节点数",
    "snat_entry": 是否配置SNATEntry,
    "endpoint_public_access":"是否公网暴露集群endpoint",
    "cpu_policy": "static|none",
    "node_port_range": "节点端口范围,默认30000-65535",
    "proxy_mode": "网络模式, 可选值iptables|ipvs",
    "addons": "选装addon, 数组格式对象", 
    "tags": "给集群打tag标签, 数组格式对象",
}
```

**请求体解释**

|名称|类型|必须|描述|
|--|--|--|--|
|cluster\_type|string|是|集群类型|
|key\_pair|string|是|keypair名称。和login\_password二选一。|
|login\_password|string|是|SSH登录密码。密码规则为8 - 30 个字符，且同时包含三项（大、小写字母，数字和特殊符号）。和key\_pair 二选一。|
|master\_instance\_charge\_type|string|是|Master节点付费类型，可选值为： -   PrePaid：预付费
-   PostPaid：按量付费

 默认为按量付费。|
|master\_instance\_types|list|是|Master节点 ECS 规格类型代码。更多详细信息，参见[../../../../dita-oss-bucket/SP\_2/DNA0011858383/ZH-CN\_TP\_9548.md\#](../../../../cn.zh-CN/实例/实例规格族.md#)。|
|master\_system\_disk\_category|string|是|Master节点系统盘类型，取值范围是： -   cloud\_efficiency：高效云盘
-   cloud\_ssd：SSD云盘

 |
|master\_system\_disk\_size|int|是|Master节点系统盘大小，单位为GiB。|
|master\_vswitch\_ids|list|是|Master节点交换机ID，取值范围为1-3。为确保集群的高可用性，推荐您选择3个交换机，且分布在不同的可用区。|
|num\_of\_nodes|int|是|Worker节点数。范围是\[0,300\]。|
|name|string|是|集群名称， 集群名称可以使用大小写英文字母、中文、数字、中划线。|
|region\_id|string|是|集群所在地域ID。|
|snat\_entry|bool|是|是否为网络配置SNAT。 -   当已有VPC能访问公网环境时，设置为 false。
-   当已有VPC不能访问公网环境时：
    -   设置为True，表示配置SNAT，此时可以访问公网环境。
    -   设置为false，表示不配置SNAT，此时不能访问公网环境。

 |
|worker\_instance\_types|list|是|Worker 节点 ECS 规格类型代码。更多详细信息，参见[实例规格族](../../../../cn.zh-CN/实例/实例规格族.md#)。|
|worker\_system\_disk\_category|string|是|Worker节点系统盘类型。|
|worker\_system\_disk\_size|int|是|Worker节点系统盘大小，单位为GiB。|
|worker\_vswitch\_ids|list|是|worker节点的虚拟交换机 ID。|
|addons|list|否|Kubernetes集群的addon插件的组合。 -   addons的参数：
    -   name：必填，addon插件的名称
    -   version：可选，取值为空时默认取最新版本
    -   config：可选，取值为空时表示无需配置
-   网络插件：包含Flannel和Terway网络插件，二选一
-   日志服务：可选，如果不开启日志服务时，将无法使用集群审计功能

 |
|container\_cidr|string|否|容器网段，不能和VPC网段冲突。当选择系统自动创建VPC时，默认使用172.16.0.0/16网段。|
|cloud\_monitor\_flags|bool|否|是否安装云监控插件： -   true：表示安装
-   false：表示不安装

 |
|cpu\_policy|string|否|CPU策略。集群版本为1.12.6及以上版本支持static 和 none两种策略。默认为none。|
|disable\_rollback|bool|否|失败是否回滚： -   true：表示失败不回滚
-   false：表示失败回滚

 默认为true，如果选择失败回滚，则会释放创建过程中所生产的资源，不推荐使用false。|
|master\_auto\_renew|bool|否|Master节点是否自动续费，当master\_instance\_charge\_type取值为PrePaid时才生效，可选值为： -   true：自动续费
-   false：不自动续费

 |
|master\_auto\_renew\_period|int|否|自动续费周期，当选择预付费和自动续费时才生效，且为必选值： -   `PeriodUnit=Week`时，取值：\{“1”, “2”, “3”\}
-   `PeriodUnit=Month`时，取值\{“1”, “2”, “3”, “6”, “12”\}

 |
|master\_count|int|否|Master实例个数，可选值3或者5个。默认值为3。|
|master\_data\_disk|bool|否|Master节点是否挂载数据盘，可选择为： -   true：挂载数据盘
-   false：不挂载数据盘，默认为false

 |
|master\_data\_disk\_category|string|否|Master数据盘类型，只有挂载数据盘的时候生效，取值范围是： -   cloud：普通云盘
-   cloud\_efficiency：高效云盘
-   cloud\_ssd：SSD云盘

 |
|master\_data\_disk\_size|int|否|Master节点数据盘大小，只有挂载数据盘的时候生效，单位为GiB。|
|master\_period|int|否|包年包月时长，当master\_instance\_charge\_type取值为PrePaid时才生效且为必选值，取值范围： -   `PeriodUnit=Week`时，Period取值：\{“1”, “2”, “3”, “4”\}
-   `PeriodUnit=Month`时，Period取值：\{ “1”, “2”, “3”, “4”, “5”, “6”, “7”, “8”, “9”, “12”, “24”, “36”,”48”,”60”\}

 |
|master\_period\_unit|string|否|当指定为PrePaid的时候需要指定周期。可选择为： -   Week：以周为计时单位
-   Month：以月为计时单位

 |
|node\_port\_range|string|否|节点服务端口。取值范围为\[30000,65535\]|
|proxy\_mode|string|否|kube-proxy 代理模式，支持 iptables 和 IPVS两种模式。 默认为 iptables 。|
|endpoint\_public\_access|bool|否|是否开启公网API Server： -   true：默认为True，表示开放公网API Server
-   false：若设置为false， 则不会创建公网的API Server，仅创建私网的API Server

 |
|service\_cidr|string|否|服务网段，不能和VPC网段以及容器网段冲突。当选择系统自动创建VPC时，默认使用172.19.0.0/20网段。|
|ssh\_flags|bool|否|是否开放公网SSH登录： -   true：表示开放
-   false：表示不开放

 |
|tags|list|否|给集群打tag标签： -   key：标签名称
-   value：标签值

 |
|timeout\_mins|int|否|集群资源栈创建超时时间，以分钟为单位，默认值 60。|
|vpcid|string|否|VPC ID，可空。如果不设置，系统会自动创建VPC，系统创建的VPC网段为192.168.0.0/16。 **说明：** VpcId 和 vswitchid 只能同时为空或者同时都设置对应的值。

 |
|worker\_auto\_renew|bool|否|是否开启Worker节点自动续费，可选值为： -   true：自动续费
-   false：不自动续费

 |
|worker\_auto\_renew\_period|int|否|自动续费周期，当选择预付费和自动续费时才生效，且为必选值： -   `PeriodUnit=Week`时，取值：\{“1”, “2”, “3”\}
-   `PeriodUnit=Month`时，取值\{“1”, “2”, “3”, “6”, “12”\}

 |
|worker\_data\_disk|string|否|是否挂载数据盘，可选择为： -   true：表示worker节点挂载数据盘
-   false：表示worker节点不挂载数据盘

 |
|worker\_data\_disk\_category|int|否|数据盘类型，只在挂载Worker节点数据盘时有效，取值范围： -   cloud：普通云盘
-   cloud\_efficiency：高效云盘
-   cloud\_ssd：SSD云盘

 |
|worker\_data\_disk\_size|string|否|数据盘大小，只有挂载数据盘的时候生效，单位为GiB。|
|worker\_instance\_charge\_type|string|否|Worker节点付费类型，可选值为： -   PrePaid：预付费
-   PostPaid：按量付费

 默认为按量付费。|
|worker\_period|int|否|包年包月时长，当worker\_instance\_charge\_type取值为PrePaid时才生效且为必选值，取值范围： -   `PeriodUnit=Week`时，Period取值：\{“1”, “2”, “3”, “4”\}
-   `PeriodUnit=Month`时，Period取值：\{ “1”, “2”, “3”, “4”, “5”, “6”, “7”, “8”, “9”, “12”, “24”, “36”,”48”,”60”\}

 |
|worker\_period\_unit|string|否|当指定为PrePaid的时候需要指定周期。可选择为： -   Week：以周为计时单位
-   Month：以月为计时单位

 |

## 返回信息 {#section_ljv_tm2_xdb .section}

**返回行 ResponseLine**

``` {#codeblock_oho_sj4_ob6}
HTTP/1.1 202 Accepted
```

**特有返回头 ResponseHead**

无，请参考[公共返回头部](cn.zh-CN/开发指南/集群 API 调用方式/公共参数.md#section_zr5_lf1_wdb)。

**返回体 ResponseBody**

|名称|类型|描述|
|--|--|--|
|cluster\_id|string|集群实例ID。|
|request\_id|string|请求ID。|
|task\_id|string|任务ID。系统自动分配，用户查询任务状态。|

## 示例 {#section_hcw_wm2_xdb .section}

**请求示例**

``` {#codeblock_csu_szx_vno}
POST /clusters HTTP/1.1
<公共请求头>
{
"cluster_type":"Kubernetes",
"name":"my-test-Kubernetes-cluster",
"region_id":"cn-beijing",
"disable_rollback":true,
"timeout_mins":60,
"kubernetes_version":"1.12.6-aliyun.1",
"snat_entry":true,
"endpoint_public_access":false,
"cloud_monitor_flags":false,
"node_cidr_mask":"25",
"proxy_mode":"iptables",
"tags":[],
"addons":[{"name":"flannel"}, {"name":"nginx-ingress-controller"}],
"node_port_range":"30000-32767",
"login_password":"test****",
"cpu_policy":"none",
"master_count":3,
"master_vswitch_ids":["vsw-2ze48rkq464rsdts****","vsw-2ze48rkq464rsdts1****","vsw-2ze48rkq464rsdts1****"],
"master_instance_types":["ecs.sn1.medium","ecs.sn1.medium","ecs.sn1.medium"],
"master_system_disk_category":"cloud_efficiency",
"master_system_disk_size":40,
"worker_instance_types":["ecs.sn2.3xlarge"],
"num_of_nodes":3,
"worker_system_disk_category":"cloud_efficiency",
"worker_system_disk_size":120,
"vpcid":"vpc-2zegvl5etah5requ0****",
"worker_vswitch_ids":["vsw-2ze48rkq464rsdts****"],
"container_cidr":"172.20.XX.XX/16",
"service_cidr":"172.21.XX.XX/20"
}
```

**返回示例**

``` {#codeblock_t8k_uc4_249}
HTTP/1.1 202 Accepted
<公共响应头>
{
    "cluster_id": "cb95aa626a47740afbf6aa099b650****",
    "request_id": "687C5BAA-D103-4993-884B-C35E4314A1E1",
    "task_id": "T-5a54309c80282e39ea00002f"
}
```

