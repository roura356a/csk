# Kubernetes 集群伸缩 {#reference_agj_fx2_xdb .reference}

增加或者删除集群中Worker节点的数量\(该操作仅限于ROS生成的资源\)。

## 请求信息 {#section_r2n_jx2_xdb .section}

**请求行 RequestLine**

```
PUT /clusters/{cluster_id} HTTP/1.1
```

**请求行参数 URI Param**

|名称|类型|是否必须|描述|
|:-|:-|:---|:-|
|cluster\_id|string|是|集群ID|

**特有请求头 RequestHead**

无，请参考[公共请求头部](intl.zh-CN/开发指南/Kubernetes API 参考/集群 API 调用方式/公共参数.md#section_mr5_lf1_wdb)。

**请求体 RequestBody**

```
{
    "disable_rollback": "失败是否回滚",
    "timeout_mins": 集群创建超时时间,
    "worker_instance_type": "Worker实例规格",
    "worker_system_disk_category": "Worker系统盘类型",
    "worker_system_disk_size": "Worker节点系统盘大小",
    "login_password": "节点SSH登录密码",    
    "num_of_nodes": "Worker节点数"
}
```

**请求体解释**

|名称|类型|必须|描述|
|:-|:-|:-|:-|
|disable\_rollback|bool|是|失败是否回滚，true 表示失败不回滚，false 表示失败回滚。如果选择失败回滚，则会释放创建过程中所生产的资源，不推荐使用 false|
|timeout\_mins|int|是|集群资源栈创建超时时间，以分钟为单位，默认值 60。|
|worker\_instance\_type|string|是|Worker 节点 ECS 规格类型代码。更多详细信息，参见[实例规格族](../../../../intl.zh-CN/产品简介/实例规格族.md#)。|
|worker\_system\_disk\_category|string|是|Worker节点系统盘类型。|
|worker\_system\_disk\_size|int|是|Worker节点系统盘大小|
|login\_password|string|是|SSH登录密码。密码规则为8 - 30 个字符，且同时包含三项（大、小写字母，数字和特殊符号）该密码必须和创建集群时的密码一致|
|num\_of\_nodes|int|是|Worker节点数。范围是\[0,300\]。如果是扩容，该值要大于已有Worker节点数；如果是缩容，则该值需要小于已有Worker节点数|

## 返回信息 {#section_w1l_gz2_xdb .section}

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

## 示例 {#section_dsh_jz2_xdb .section}

**请求示例**

```
PUT /clusters/Cccfd68c474454665ace07efce924f75f HTTP/1.1
<公共请求头>
{
    "disable_rollback": true,
    "timeout_mins": 60,
    "worker_instance_type": "ecs.sn1ne.large",
    "worker_system_disk_category": "cloud_efficiency",
    "worker_system_disk_size": 40,
    "login_password": "Hello1234",
    "num_of_nodes": 8
}
```

**返回示例**

```
HTTP/1.1 202 Accepted
<公共响应头>
{
    "cluster_id": "Cccfd68c474454665ace07efce924f75f",
    "request_id": "687C5BAA-D103-4993-884B-C35E4314A1E1",
    "task_id": "T-5a54309c80282e39ea00002f"
}
```

