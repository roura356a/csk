# 批量查询集群Addons升级状态

调用DescribeClusterAddonsUpgradeStatus同时查询多个组件的升级状态。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=CS&api=DescribeClusterAddonsUpgradeStatus&type=ROA&version=2015-12-15)

## 请求头

该接口使用公共请求头，无特殊请求头。请参见公共请求参数文档。

## 请求语法

```
GET /clusters/[ClusterId]/components/upgradestatus 
```

## 请求参数

|名称|类型|位置|是否必选|示例值|描述|
|--|--|--|----|---|--|
|ClusterId|String|Path|是|c23421cfa74454bc8b37163fd19af\*\*\*\*|集群ID。 |
|componentIds|Array of String|Query|否|metric-server|组件名。 |

## 示例

请求示例

```
GET /clusters/c23421cfa74454bc8b37163fd19af****/components/upgradestatus?componentIds=["metric-server"]
公共请求头
```

## 错误码

访问[错误中心](https://error-center.aliyun.com/status/product/CS)查看更多错误码。

