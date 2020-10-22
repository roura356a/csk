# 升级集群Addons

调用UpgradeClusterAddons将指定组件升级到指定版本。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=CS&api=UpgradeClusterAddons&type=ROA&version=2015-12-15)

## 请求头

该接口使用公共请求头，无特殊请求头。请参见公共请求参数文档。

## 请求语法

```
POST /clusters/{ClusterId}/components/upgrade HTTP|HTTPS
```

## 请求参数

|名称|类型|位置|是否必选|示例值|描述|
|--|--|--|----|---|--|
|ClusterId|String|Path|是|ack-arena|集群ID。 |
| |Array|Body|否| |请求体。 |
|component\_name|String|Body|否|ar|组件名称。 |
|next\_version|String|Body|否|0.5.0|可升级版本。 |
|version|String|Body|否|0.5.0|当前版本。 |

## 示例

请求示例

```
POST /clusters/{ClusterId}/components/upgrade HTTP/1.1
公共请求头
{
    "ClusterId":"cdf4994652bee42789be1d9964fa0****",
    "component_name":"ack-arena",
    "next_version":"0.5.0",
    "version":"0.5.0"
}
```

## 错误码

访问[错误中心](https://error-center.alibabacloud.com/status/product/CS)查看更多错误码。

