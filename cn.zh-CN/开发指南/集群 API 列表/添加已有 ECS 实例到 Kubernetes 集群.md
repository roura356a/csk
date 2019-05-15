# 添加已有 ECS 实例到 Kubernetes 集群 {#reference_hrl_33b_wdb .reference}

添加已有实例到集群。

**说明：** 添加过程中会替换系统盘，需要提前做好数据备份。

## 请求信息 {#section_e2b_mjb_wdb .section}

**请求行 RequestLine**

```
POST /clusters/{cluster_id}/attach HTTP/1.1
```

**请求行参数 URI Param**

|名称|类型|是否必须|描述|
|:-|:-|:---|:-|
|cluster\_id|string|是|集群 ID|

**特有请求头 RequestHead**

无，请参考[公共请求头部](intl.zh-CN/开发指南/集群 API 调用方式/公共参数.md#section_mr5_lf1_wdb)。

**请求体 RequestBody**

```
{
    "password": "ECS 实例 root 登录密码",
    "instances": "要添加的实例数组",
    "format_disk": "是否格式化数据盘",
    "key_pair": "密钥对"
}
```

**请求体解析**

|名称|类型|是否必须|描述|
|:-|:-|:---|:-|
|password|String|是|ECS 实例密码。密码规则为8 - 30 个字符，且同时包含三项（大、小写字母，数字和特殊符号）|
|instances|Array|是|已有实例的数组。|
|format\_disk|bool|否|是否格式化数据盘。|
|key\_pair|string|否| 密钥对名称。

-   Windows实例，忽略该参数。默认为空。即使填写了该参数，仍旧只执行 Password 的内容。
-   Linux实例的密码登录方式会被初始化成禁止。

 |

## 返回信息 {#section_awy_llb_wdb .section}

**返回行 ResponseLine**

```
HTTP/1.1 202 OK
```

**特有返回头 ResponseHead**

无，请参考[公共返回头部](intl.zh-CN/开发指南/集群 API 调用方式/公共参数.md#section_zr5_lf1_wdb)。

**返回体 ResponseBody**

```
{
    "list": [
        {
            "code": "200",
            "instanceId": "i-2zee3oiwcyoz7kwdo8bt",
            "message": "successful"
        },
        {
            "code": "200",
            "instanceId": "i-2ze0lgm3y6iylcbtcypf",
            "message": "successful"
        }
    ],
    "task_id": "T-5a544aff80282e39ea000039"
}
```

## 示例 {#section_t2q_rlb_wdb .section}

**请求示例**

```
POST /clusters/Cccfd68c474454665ace07efce924f75f/attach HTTP/1.1
<公共请求头>
{
    "password": "Hello1234",
    "instances": [
        "i-xxxx",
        "i-yyyy"
    ]
}
```

**返回示例**

```
HTTP/1.1 202 Accepted
<公共响应头>
{
    "list": [
        {
            "code": "200",
            "instanceId": "i-xxxx",
            "message": "successful"
        },
        {
            "code": "200",
            "instanceId": "i-yyyy",
            "message": "successful"
        }
    ],
    "task_id": "T-5a544aff80282e39ea000039"
}
```

