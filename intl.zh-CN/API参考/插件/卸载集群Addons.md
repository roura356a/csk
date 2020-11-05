# 卸载集群Addons

调用UnInstallClusterAddons卸载指定集群的Addons。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=CS&api=UnInstallClusterAddons&type=ROA&version=2015-12-15)

## 请求头

该接口使用公共请求头，无特殊请求头。请参见公共请求参数文档。

## 请求语法

```
POST /clusters/{ClusterId}/components/uninstall HTTP|HTTPS
```

## 请求参数

|名称|类型|位置|是否必选|示例值|描述|
|--|--|--|----|---|--|
|ClusterId|String|Path|是|c5b5e80b0b64a4bf6939d2d8fbbc5\*\*\*\*|集群ID。 |
| |Array|Body|否| |Addon列表，结构为数组。 |
|name|String|Body|否|ack-node-problem-detector|Addon名称。 |

## 示例

请求示例

```
POST /clusters/[ClusterId]/components/uninstall HTTP/1.1
公共请求头
{
    "ClusterId":"c5b5e80b0b64a4bf6939d2d8fbbc5****"
}
```

请求示例补充说明

```
POST /clusters/c3fb96524f9274b4495df0f12a6b5****/components/uninstall?RegionId=cn-hangzhou --header "Content-Type=application/json;" --body '[{"name":"ack-node-problem-detector"}]'
```

## 错误码

访问[错误中心](https://error-center.alibabacloud.com/status/product/CS)查看更多错误码。

