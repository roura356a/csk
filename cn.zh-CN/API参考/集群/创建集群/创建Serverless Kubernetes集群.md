# 创建Serverless Kubernetes集群

调用CreateCluster创建一个新的Serverless Kubernetes集群实例。

## 请求信息

请求行RequestLine

```
POST /clusters HTTP/1.1
```

特有请求头RequestHead

无。请参见[公共请求和返回结果](/cn.zh-CN/API参考/公共请求和返回结果.md)。

请求体RequestBody

```
{
"cluster_type": "Ask", 
"name": "集群名称",
"region_id": "地域",
"zoneid": "可用区",
"nat_gateway": "true", //是否创建NAT网关。
"private_zone": true, //是否开启privateZone用于服务发现。
"vpc_id": "VPC ID", //不填则自动创建专有网络VPC。
"tags": "给集群打tag标签, 数组格式对象。",
"vswitch_id": "交换机ID。"
}
```

|名称|类型|必须|描述|
|--|--|--|--|
|cluster\_type|string|是|集群类型。|
|name|string|是|集群名称， 集群名称可以使用大小写英文字母、中文、数字、中划线。|
|kubernetes\_version|string|否|集群版本号。|
|private\_zone|bool|是|是否开启PrivateZone用于服务发现，取值为true或者false。请参见[Serverless集群基于云解析PrivateZone的服务发现](/cn.zh-CN/Serverless Kubernetes集群用户指南/应用管理/Serverless集群基于云解析PrivateZone的服务发现.md)。|
|region\_id|string|是|集群所在地域ID。|
|endpoint\_public\_access|bool|否|是否开启API Server公网可访问。|
|zone\_id|string|是|所属地域的可用区。|
|vswitch\_id|string|否|交换机ID，可空。若不设置，系统会自动创建交换机，系统自动创建的交换机网段为192.168.0.0/16。|
|tags|list|否|给集群打tag标签： -   key：标签名称。
-   value：标签值。 |
|deletion\_protection|bool|否|是否开启集群删除保护。|
|addons|Array|否|Kubernetes集群的安装的组件列表。Serverless Kubernetes类型集群只支持日志相关组件：-   addons的参数：
    -   name：必填，组件名称。
    -   config：可选，取值为空时表示无需配置。
    -   disabled：可选，是否禁止默认安装。
-   日志组件：可选，如果不开启日志服务时，将无法使用集群审计功能：
    -   创建新的`sls project`：\[\{"name":"logtail-ds","config":""\}\]
    -   使用已有`sls project`：\[\{"name":"logtail-ds","config":"\{"sls\_project\_name":"your\_sls\_project\_name"\}"\}\] |
|nat\_gateway|bool|否|是否创建NAT网关。取值为true或者false。如果不设置，系统默认为false。|
|vpc\_id|string|否|VPC ID，可空。如果不设置，系统会自动创建VPC，系统创建的VPC网段为192.168.0.0/16。 **说明：** vpc\_id和vswitch\_ids只能同时为空或者同时都设置对应的值。 |
|vswitch\_ids|list|否|虚拟交换机列表，可空。如果不设置，系统会自动创建。**说明：** vpc\_id和vswitch\_ids只能同时为空或者同时设置对应的值。 |
|security\_group\_id|string|否|安全组ID，如果不设置，系统会自动创建。|

## 返回信息

返回行ResponseLine

```
HTTP/1.1 202 Accepted
```

特有返回头ResponseHead

无。请参见[公共请求和返回结果](/cn.zh-CN/API参考/公共请求和返回结果.md)。

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
    "cluster_type":"Ask",
    "name":"ask-cluster",
    "kubernetes_version":"1.16.9-aliyun.1",
    "region_id":"cn-shenzhen",
    "endpoint_public_access":true,
    "private_zone":true,
    "tags":[
        {
            "key":"tier",
            "value":"frontend"
        }
    ],
    "deletion_protection":true,
    "addons":[
        {
            "name":"logtail-ds"
        }
    ],
    "zone_id":"cn-shenzhen-a",
    "vpc_id":"vpc-wz984yvbd6lck22z3****",
    "vswitch_ids":[
        "vsw-wz9uwxhawmtzg7u9h****"
    ],
    "logging_type":"SLS",
    "security_group_id":"sg-wz9b86l4s7nthi1k****"
}
```

创建ASK集群时使用已有VPC。请求示例如下。

```
POST /clusters HTTP/1.1
<公共请求头>
{
    "cluster_type":"Ask",
    "name":"test-ask",
    "kubernetes_version":"1.16.9-aliyun.1",
    "region_id":"cn-hangzhou",
    "endpoint_public_access":false,
    "private_zone":false,
    "nat_gateway":false,
    "tags":[
        {
            "key":"k-aa",
            "value":"v-aa"
        }
    ],
    "deletion_protection":false,
    "addons":[
        {
            "name":"logtail-ds"
        }
    ],
    "zone_id":"cn-hangzhou-i"
}
```

返回示例

```
HTTP/1.1 202 Accepted
<公共响应头>
{
    "cluster_id": "cb95aa626a47740afbf6aa099b650****",
    "RequestId": "687C5BAA-D103-4993-884B-C35E4314A1E1",
    "task_id": "T-5a54309c80282e39ea00002f",
}
```

