# 修改集群tag接口

调用ModifyClusterTags修改当前Kubernetes集群的tag接口。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=CS&api=ModifyClusterTags&type=ROA&version=2015-12-15)

## 请求头

该接口使用公共请求头，无特殊请求头。请参见公共请求和返回结果文档。

## 请求语法

```
POST /clusters/[ClusterId]/tags HTTPS|HTTP
```

## 请求参数

|名称|类型|是否必选|示例值|描述|
|--|--|----|---|--|
|ClusterId|String|是|c106f377e16f34eb1808d6b9362c9\*\*\*\*|集群ID。 |
|key|String|是|type|标签名称。 |
|value|String|是|web|标签值。 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|requestId|String|687C5BAA-D103-4993-884B-C35E4314A1E1|请求ID。 |

## 示例

请求示例

```
POST /clusters/[ClusterId]/tags HTTP/1.1
公共请求头
[
  {
    "key": "xxxxxxxx",
    "value": "xxxxxxxx"
  }
]
```

正常返回示例

`XML` 格式

```
<cluster_id>cb95aa626a47740afbf6aa099b650****</cluster_id>
<request_id>687C5BAA-D103-4993-884B-C35E4314A1E1</request_id>
<task_id>T-5a54309c80282e39ea00002f</task_id>
```

`JSON` 格式

```
{
    "cluster_id": "cb95aa626a47740afbf6aa099b650****",
    "request_id": "687C5BAA-D103-4993-884B-C35E4314A1E1",
    "task_id": "T-5a54309c80282e39ea00002f"
}
```

## 错误码

访问[错误中心](https://error-center.alibabacloud.com/status/product/CS)查看更多错误码。

