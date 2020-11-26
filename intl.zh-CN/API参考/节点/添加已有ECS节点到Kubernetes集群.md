# 添加已有ECS节点到Kubernetes集群

调用AttachInstances添加已有实例到集群。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=CS&api=AttachInstances&type=ROA&version=2015-12-15)

## 请求头

该接口使用公共请求头，无特殊请求头。请参见公共请求参数文档。

## 请求语法

```
POST /clusters/{ClusterId}/attach 
```

## 请求参数

|名称|类型|位置|是否必选|示例值|描述|
|--|--|--|----|---|--|
|ClusterId|String|Path|是|c106f377e16f34eb1808d6b9362c9\*\*\*\*|集群ID。 |
| |Object|Body|否| |实例列表。 |
|cpu\_policy|String|Body|否|none|CPU管理策略。 |
|format\_disk|Boolean|Body|否|false|是否格式化数据盘。 |
|image\_id|String|Body|否|aliyun\_2\_1903\_x64\_20G\_alibase\_20200529.vhd|自定义镜像ID。 |
|instances|Array of String|Body|否|i-2zed0sswuau6o89b\*\*\*\*|实例ID。 |
|is\_edge\_worker|Boolean|Body|否|false|是否为边缘节点，即ens节点。 |
|keep\_instance\_name|Boolean|Body|否|true|保留主机名。取值：true和false。 |
|key\_pair|String|Body|否|\*\*\*|免密登录密钥对名称，和login\_password二选一。 |
|nodepool\_id|String|Body|否|np615c0e0966124216a0412e10afe0\*\*\*\*|节点池ID。 |
|password|String|Body|否|Hello1234|SSH登录密码，和key\_pair二选一。 |
|rds\_instances|Array of String|Body|否|rds-xxx|RDS实例ID。 |
|runtime|Object|Body|否| |容器运行时配置。 |
|name|String|Body|否|docker|容器运行时名称。 |
|version|String|Body|否|19.03.5|容器运行时版本。 |
|tags|Array|Body|否| |标签配置。 |
|key|String|Body|否|k-aa|标签key。 |
|value|String|Body|否|v-aa|标签值。 |
|user\_data|String|Body|否|\*\*\*|自定义节点数据。 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|list|Array of list| |节点信息列表。 |
|code|String|200|结果状态码。 |
|instanceId|String|i-2ze0lgm3y6iylcbt\*\*\*\*|节点实例ID。 |
|message|String|successful|添加结果描述信息。 |
|task\_id|String|T-5a544aff80282e39ea000039|任务ID。 |

## 示例

请求示例

```
POST /clusters/[ClusterId]/attach HTTP/1.1
公共请求头
{
    "ClusterId": "c106f377e16f34eb1808d6b9362c9****",
    "instances":[
        "i-wz9e8pvnjalxkggk****"
    ],
    "format_disk":true,
    "image_id":"centos_7_7_x64_20G_alibase_20191225.vhd",
    "rds_instances":[
        "rds1",
        "rds2"
    ],
    "user_data":"ZWNobyAxMjM0NTY=",
    "tags":[
        {
            "key":"foo",
            "value":"bar"
        }
    ],
    "keep_instance_name":true,
    "cpu_policy":"none",
    "runtime":{
        "name":"docker",
        "version":"19.03.5"
    },
    "password":"Hello@1234"
}
```

正常返回示例

`XML` 格式

```
<task_id>T-5a544aff80282e39ea000039</task_id>
<list>
    <code>200</code>
    <instanceId>i-2ze0lgm3y6iylcbt****</instanceId>
    <message>successful</message>
</list>
```

`JSON` 格式

```
{
    "task_id": "T-5a544aff80282e39ea000039",
    "list": [
        {
            "code": "200",
            "instanceId": "i-2ze0lgm3y6iylcbt****",
            "message": "successful"
        }
    ]
}
```

## 错误码

访问[错误中心](https://error-center.alibabacloud.com/status/product/CS)查看更多错误码。

