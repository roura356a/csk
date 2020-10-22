# 取消升级集群Addons

调用CancelComponentUpgrade取消集群组件升级。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=CS&api=CancelComponentUpgrade&type=ROA&version=2015-12-15)

## 请求头

该接口使用公共请求头，无特殊请求头。请参见公共请求参数文档。

## 请求语法

```
POST /clusters/{clusterid}/components/{componentid}/cancel HTTP|HTTPS
```

## 请求参数

|名称|类型|位置|是否必选|示例值|描述|
|--|--|--|----|---|--|
|clusterid|String|Path|是|c82e6987e2961451182edacd74faf\*\*\*\*|集群ID。 |
|componentid|String|Path|是|metric-server|组件ID。 |

## 示例

请求示例

```
POST /clusters/[clusterid]/components/[componentid]/cancel HTTP/1.1
公共请求头
{
    "clusterid":"c82e6987e2961451182edacd74faf****",
    "componentid":"metric-server"
}
```

## 错误码

访问[错误中心](https://error-center.alibabacloud.com/status/product/CS)查看更多错误码。

