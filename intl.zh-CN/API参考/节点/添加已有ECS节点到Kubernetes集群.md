# 添加已有ECS节点到Kubernetes集群

调用AttachInstances添加已有实例到集群。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=CS&api=AttachInstances&type=ROA&version=2015-12-15)

## 请求头

该接口使用公共请求头，无特殊请求头。请参见公共请求参数文档。

## 请求语法

```
 http://provision.cs.aliyun-inc.com http
```

## 请求参数

|名称|类型|是否必选|示例值|描述|
|--|--|----|---|--|
|ClusterId|String|是|c106f377e16f34eb1808d6b9362c9\*\*\*\*|集群ID。 |
|instances|Array of String|是|i-0xi187lghfcy5t2d\*\*\*\*|实例ID。 |
|runtime|Object|否|true|是否保留实例名称。 |
|name|String|否|docker|容器运行时名称。 |
|version|String|否|19.03.5|容器运行时版本。 |
|image\_id|String|否|centos\_7\_7\_x64\_20G\_alibase\_20191225.vhd|自定义镜像，默认使用系统镜像。当选择自定义镜像时，将取代默认系统镜像。请参见[自定义镜像](https://help.aliyun.com/document_detail/146647.html)。 |
|format\_disk|Boolean|否|false|是否格式化数据盘。 |
|keep\_instance\_name|Boolean|否|true|是否保留实例名称。 |
|cpu\_policy|String|否|none|CPU策略。Kubernetes集群版本为1.12.6及以上版本支持static和none两种策略，默认为none。 |
|key\_pair|String|否|secrity-key|key\_pair名称，与login\_password二选一 |
|password|String|否|Hello@1234|扩容的worker节点密码。密码规则为8~30 个字符，且同时包含三项（大、小写字母，数字和特殊符号），和key\_pair二选一。 |
|is\_edge\_worker|Boolean|否|false|是否为边缘节点。接入ENS节点时需要配置为true。 |
|user\_data|String|否|""|RDS实例列表，将该ECS加入到选择的RDS实例的白名单中。 |
|nodepool\_id|String|否|npb9d542d191c54253bb4488bdc4fe\*\*\*\*|节点标签。 |
|rds\_instances|Array of String|否|rm-2zev748xi27xc\*\*\*\*|RDS实例。 |
|tags|Array|否| |节点标签。 |
|key|String|否|tier|标签键。 |
|value|String|否|frontend|标签值。 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|list|Array of list| |节点信息列表。 |
|code|String|"200"|结果状态码。 |
|instanceId|String|"i-2ze0lgm3y6iylcbt\*\*\*\*"|节点实例ID。 |
|message|String|"successful"|添加结果描述信息。 |
|task\_id|String|"T-5a544aff80282e39ea000039"|任务ID。 |

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

