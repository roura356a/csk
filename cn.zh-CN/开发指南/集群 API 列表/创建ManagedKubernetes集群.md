# 创建ManagedKubernetes集群 {#reference_af2_kc1_dfb .reference}

创建一个新的ManagedKubernetes集群实例，并创建指定数量的节点。

## 请求信息 {#section_dt4_pl2_xdb .section}

**请求行 RequestLine**

```
POST /clusters HTTP/1.1 
```

**特有请求头 RequestHead**

无，请参考[公共请求头部](intl.zh-CN/开发指南/集群 API 调用方式/公共参数.md#section_mr5_lf1_wdb)。

**请求体 RequestBody**

```
{
	"disable_rollback": "失败是否回滚",
	"name": "集群名称",
	"timeout_mins": 集群创建超时时间,
	"cluster_type": "集群类型，ManagedKubernetes",
	"region_id": "地域",
	"vpcid": "VPC ID",
	"zoneid": "可用区",
	"vswitchid": "交换机ID",	
	"container_cidr": "容器POD CIDR",
	"service_cidr": "服务CIDR",
	"cloud_monitor_flags":"是否安装云监控插件",
	"login_password": "节点SSH登陆密码，和key_pair二选一",
	"key_pair":"keypair名称，和login_password 二选一",
	"worker_instance_charge_type":"Worker节点付费类型PrePaid|PostPaid",
	"worker_period_unit":"包年包月单位，Month,Year，只有在PrePaid下生效",
	"worker_period":"包年包月时长，只有在PrePaid下生效",
	"worker_auto_renew":"Worker节点自动续费true|false",
	"worker_auto_renew_period":"Worker节点续费周期",
	"worker_instance_type": "Worker实例规格",
	"worker_system_disk_category": "Worker系统盘类型",
	"worker_system_disk_size": "Worker节点系统盘大小",
	"worker_data_disk":"是否挂载数据盘 true|false",
	"worker_data_disk_category":"数据盘类型",
	"worker_data_disk_size":"数据盘大小",
	"num_of_nodes": "Worker节点数",
	"snat_entry": 是否配置SNATEntry,
        "public_slb":"是否创建公网API Server对应的EIP",
}
```

**请求体解释**

|名称|类型|必须|描述|
|--|--|--|--|
|disable\_rollback|bool|否|失败是否回滚：-   true：表示失败不回滚
-   false：表示失败回滚

如果选择失败回滚，则会释放创建过程中所生产的资源，不推荐使用false|
|name|string|是|集群名称，集群名称可以使用大小写英文字母、中文、数字、中划线|
|timeout\_mins|int|否|集群资源栈创建超时时间，以分钟为单位，默认值 60分钟|
|region\_id|string|是|集群所在地域ID|
|zoneid|string|是|所属地域的可用区|
|vpcid|string|否|VPC ID，可空。如果不设置，系统会自动创建VPC，系统创建的VPC网段为192.168.0.0/16。 VpcId 和 vswitchid 只能同时为空或者同时都设置相应的值|
|vswitchid|string|否|交换机ID，可空。若不设置，系统会自动创建交换机，系统自定创建的交换机网段为 192.168.0.0/16|
|container\_cidr|string|否|容器网段，不能和VPC网段冲突。当选择系统自动创建VPC时，默认使用172.16.0.0/16网段|
|service\_cidr|string|否|服务网段，不能和VPC网段以及容器网段冲突。当选择系统自动创建VPC时，默认使用172.19.0.0/20网段|
|worker\_instance\_charge\_type|string|否|Worker节点付费类型，可选值为：-   PrePaid：预付费
-   PostPaid：按量付费

|
|worker\_period\_unit|string|否|当指定为PrePaid的时候需要指定周期。可选择为：-   Week：以周为计时单位
-   Month：以月为计时单位

|
|worker\_period|int|否|包年包月时长，当worker\_instance\_charge\_type取值为PrePaid时才生效且为必选值，取值范围：-   `PeriodUnit=Week`时，Period取值：\{“1”, “2”, “3”, “4”\}，
-   `PeriodUnit=Month`时，Period取值：\{ “1”, “2”, “3”, “4”, “5”, “6”, “7”, “8”, “9”, “12”, “24”, “36”,”48”,”60”\}

|
|worker\_auto\_renew|bool|否|是否开启Worker节点自动续费，可选值为：-   true：自动续费
-   false：不自动续费

|
|worker\_auto\_renew\_period|int|否|自动续费周期，当worker\_instance\_charge\_type取值为PrePaid时才生效且为必选值：-   `PeriodUnit=Week`时，取值：\{“1”, “2”, “3”\}，
-   `PeriodUnit=Month`时，取值\{“1”, “2”, “3”, “6”, “12”\}

|
|worker\_data\_disk|string|否|是否挂载数据盘，可选择：-   true：表示worker节点挂载数据盘
-   false：表示worker节点不挂载数据盘

|
|worker\_data\_disk\_category|int|否|数据盘类型|
|worker\_data\_disk\_size|string|否|数据盘大小|
|worker\_instance\_type|string|是|Worker 节点 ECS 规格类型代码。更多详细信息，参见[../../../../../dita-oss-bucket/SP\_2/DNA0011858383/ZH-CN\_TP\_9548.md\#](../../../../../intl.zh-CN/实例/实例规格族/实例规格族汇总.md#)|
|worker\_system\_disk\_category|string|是|Worker节点系统盘类型|
|worker\_system\_disk\_size|int|是|Worker节点系统盘大小|
|login\_password|string|是|SSH登录密码。密码规则为8 - 30 个字符，且同时包含三项（大、小写字母，数字和特殊符号）。和key\_pair 二选一|
|key\_pair|string|是|keypair名称。与login\_password二选一|
|num\_of\_nodes|int|是|Worker节点数。范围是\[0,300\]|
|snat\_entry|bool|是|是否为网络配置SNAT。如果是自动创建VPC必须设置为true。如果使用已有VPC则根据是否具备出网能力来设置|
|cloud\_monitor\_flags|bool|否|是否安装云监控插件|
|public\_slb|bool|否|是否开启公网API Server：-   true：默认为True，表示开放公网API Server
-   false：若设置为false， 则不会创建公网的API Server，仅创建私网的API Server

|

## 返回信息 {#section_ljv_tm2_xdb .section}

**返回行 ResponseLine**

```
HTTP/1.1 202 Accepted
```

**特有返回头 ResponseHead**

无，请参考[公共返回头部](intl.zh-CN/开发指南/集群 API 调用方式/公共参数.md#section_zr5_lf1_wdb)。

**返回体 ResponseBody**

```
{
"cluster_id":"string",
"request_id":"string",
"task_id":"string"
}
```

## 示例 {#section_hcw_wm2_xdb .section}

**请求示例**

```
POST /clusters HTTP/1.1
<公共请求头>
{
"disable_rollback": true,
"name": "my-test-Kubernetes-cluster",
"timeout_mins": 60,
"cluster_type": "ManagedKubernetes",
"region_id": "cn-beijing",
"zoneid": "cn-beijing-f",
"vpcid": "",
"vswitchid": "",
"num_of_nodes": 1,
"container_cidr": "172.16.0.0/16",
"service_cidr": "172.19.0.0/20",
"cloud_monitor_flags": true,
"master_instance_type": "ecs.sn1ne.large",
"master_system_disk_category": "cloud_efficiency",
"master_system_disk_size": 40,

"worker_instance_type": "ecs.sn1ne.large",
"worker_system_disk_category": "cloud_efficiency",
"worker_system_disk_size": 40,

"snat_entry": true,
"login_password": "Hello1234"

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

