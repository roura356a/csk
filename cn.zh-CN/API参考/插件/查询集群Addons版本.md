# 查询集群Addons版本

调用DescribeClusterAddonsVersion查询指定集群安装的所有Addons的信息。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=CS&api=DescribeClusterAddonsVersion&type=ROA&version=2015-12-15)

## 请求头

该接口使用公共请求头，无特殊请求头。请参见公共请求参数文档。

## 请求语法

```
GET /clusters/[ClusterId]/components/version HTTPS|HTTP
```

## 请求参数

|名称|类型|是否必选|示例值|描述|
|--|--|----|---|--|
|ClusterId|String|是|c82e6987e2961451182edacd74faf\*\*\*\*|集群ID。 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|can\_upgrade|Boolean|false|Addon是否可升级。 |
|changed|String|""|是否更新过。 |
|component\_name|String|alicloud-disk-controller|组件名称。 |
|message|String|ok to upgrade|是否可升级的额外说明信息。 |
|next\_version|String|""|下一个可升级版本。 |
|required|Boolean|true|是否为必需组件。 |
|template|String|\*\*\*\*|最新模板文件。 |
|version|String|v1.14.8.37-bd3fd891-aliyun|当前版本。 |

## 示例

请求示例

```
GET /clusters/[ClusterId]/components/version HTTP/1.1
公共请求头
{
"ClusterId": "c82e6987e2961451182edacd74faf****"
}
```

正常返回示例

`XML` 格式

```
<template>****</template>
<component_name>alicloud-disk-controller</component_name>
<can_upgrade>false</can_upgrade>
<message>ok to upgrade</message>
<version>v1.14.8.37-bd3fd891-aliyun</version>
<required>true</required>
<next_version>""</next_version>
<changed>""</changed>
```

`JSON` 格式

```
{"template":"****","component_name":"alicloud-disk-controller","can_upgrade":"false","message":"ok to upgrade","version":"v1.14.8.37-bd3fd891-aliyun","required":"true","next_version":"\"\"","changed":"\"\""}
```

## 错误码

访问[错误中心](https://error-center.aliyun.com/status/product/CS)查看更多错误码。

