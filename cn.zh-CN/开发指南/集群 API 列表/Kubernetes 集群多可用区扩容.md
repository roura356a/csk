# Kubernetes 集群多可用区扩容 {#reference_agj_fx2_xdb .reference}

增加集群中Worker节点的数量（支持多可用区实例）。

## 请求信息 {#section_r2n_jx2_xdb .section}

**请求行 RequestLine**

``` {#codeblock_bco_6ar_7pp}
POST /api/v2/clusters/{cluster_id} HTTP/1.1
```

**请求行参数 URI Param**

|名称|类型|是否必须|描述|
|:-|:-|:---|:-|
|cluster\_id|string|是|集群ID|

**特有请求头 RequestHead**

无，请参考[公共请求头部](intl.zh-CN/开发指南/集群 API 调用方式/公共参数.md#section_mr5_lf1_wdb)。

**请求体 RequestBody**

``` {#codeblock_avz_u1v_pj5}
{
"key_pair":"keypair名称，和login_password 二选一",
"vswitch_ids": "一台或多台虚拟交换机 ID，N 的取值范围为 [1, 3]",
"worker_instance_types": "Worker实例规格多实例规格参数",
"worker_system_disk_category": "Worker系统盘类型",
"worker_system_disk_size": "Worker节点系统盘大小",
"worker_data_disk":"是否挂载数据盘 true|false",
"tags": "给集群打tag标签, 数组格式对象",
"count":"扩容的数量"
}
```

**请求体解释**

|名称|类型|必须|描述|
|:-|:-|:-|:-|
|count|int|是|扩容数量|
|vswitch\_ids|list|是|Worker 节点的交换机ID。|
|worker\_data\_disk|bool|是|是否挂载数据盘，可选择为： -   true：表示worker节点挂载数据盘
-   false：表示worker节点不挂载数据盘

 |
|worker\_instance\_types|list|是|Worker 节点 ECS 规格类型代码。更多详细信息，参见[实例规格族](../../../../intl.zh-CN/实例/实例规格族.md#)。|
|key\_pair|string|否|keypair名称。与login\_password二选一|
|login\_password|string|否|扩容的worker节点密码。密码规则为8 - 30 个字符，且同时包含三项（大、小写字母，数字和特殊符号）。和key\_pair 二选一。|
|worker\_system\_disk\_category|string|否|Worker节点系统盘类型。|
|worker\_system\_disk\_size|int|否|Worker节点系统盘大小，单位为GiB。|

## 返回信息 {#section_w1l_gz2_xdb .section}

**返回行 ResponseLine**

``` {#codeblock_hgz_owu_3ez}
HTTP/1.1 202 Accepted
```

**特有返回头 ResponseHead**

无，请参考[公共返回头部](intl.zh-CN/开发指南/集群 API 调用方式/公共参数.md#section_zr5_lf1_wdb)。

**返回体 ResponseBody**

``` {#codeblock_5dv_rs8_f7w}
{
    "cluster_id":"string",
    "request_id":"string",
    "task_id":"string"
}
```

## 示例 {#section_dsh_jz2_xdb .section}

**请求示例**

``` {#codeblock_mbk_pyv_1no}
POST /api/v2/clusters/Cccfd68c474454665ace07efce924**** HTTP/1.1
<公共请求头>
{
    "key_pair":"common", // KeyPair 
    "vswitch_ids":["vsw-uf684tfrpwup8gcsw****"],
    "worker_instance_types"["ecs.c5.xlarge"],
    "worker_system_disk_category":"cloud_efficiency",
    "worker_system_disk_size":120,
    "worker_data_disk":false,
    "tags":[],
    "count":1
}
```

**返回示例**

``` {#codeblock_efe_vh2_3qr}
HTTP/1.1 202 Accepted
<公共响应头>
{
    "cluster_id": "Cccfd68c474454665ace07efce924****",
    "request_id": "687C5BAA-D103-4993-884B-C35E4314A1E1",
    "task_id": "T-5a54309c80282e39ea00002f"
}
```

