# 修改Managed集群的Addon参数

调用ModifyClusterConfiguration修改托管侧Addon的参数，下发ConfigMap到托管侧。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=CS&api=ModifyClusterConfiguration&type=ROA&version=2015-12-15)

## 请求头

该接口使用公共请求头，无特殊请求头。请参见公共请求参数文档。

## 请求语法

```
PUT /clusters/{ClusterId}/configuration 
```

## 请求参数

|名称|类型|位置|是否必选|示例值|描述|
|--|--|--|----|---|--|
|ClusterId|String|Path|是|cc0f87de0b8fb403f86e10e204f83\*\*\*\*|集群ID。 |
| |Object|Body|否| |请求体参数。 |
|configs|Object|Body|否| |配置信息。 |
|key|String|Body|否|k-aa|key。 |
|value|String|Body|否|v-aa|value。 |
|name|String|Body|否|metric-server|Addon名称。 |

## 示例

请求示例

```
PUT /clusters/cc0f87de0b8fb403f86e10e204f83****/configuration
公共请求头
{
    "configs":{
        "value":"v-aa",
        "key":"k-aa"
    },
    "name":"metric-server"
}
```

## 错误码

访问[错误中心](https://error-center.alibabacloud.com/status/product/CS)查看更多错误码。

