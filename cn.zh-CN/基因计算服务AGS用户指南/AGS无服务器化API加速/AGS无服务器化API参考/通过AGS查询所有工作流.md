# 通过AGS查询所有工作流

调用DescribeWorkflows查询已创建的所有工作流。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=CS&api=DescribeWorkflows&type=ROA&version=2015-12-15)

## 请求语法

```
GET /gs/workflows HTTP/1.1
Content-Type:application/json

公共请求参数
```

## 请求参数

无请求参数

## 响应体语法

```
HTTP/1.1 200 OK
Content-Type:application/json

{
  "jobs" : [ {
    "cluster_id" : "String",
    "job_name" : "String",
    "create_time" : "String"
  } ]
}
```

## 响应参数

|参数名称|类型|示例|说明|
|----|--|--|--|
|jobs|Array of job| |Job列表。 |
|cluster\_id|String|cb1a7214cfc0b41d9bb086affc2d8f51c|集群ID。 |
|job\_name|String|wgs-gpu-qb4dk|工作流名称。 |
|create\_time|String|2020-01-15T13:18:52Z|工作流创建时间。 |

## 查询所有工作流

```
GET /gs/workflows HTTP/1.1
Host:cs.aliyuncs.com
Content-Type:application/json

公共请求参数
```

正常返回示例

`XML`格式

```
HTTP/1.1 200 OK
Content-Type:application/xml

<jobs>
    <cluster_id>cb1a7214cfc0b41d9bb086affc2d8f51c</cluster_id>
    <job_name>wgs-gpu-qb4dk</job_name>
    <create_time>2020-01-15T13:18:52Z</create_time>
</jobs>
```

`JSON`格式

```
HTTP/1.1 200 OK
Content-Type:application/json

{
  "jobs" : [ {
    "cluster_id" : "cb1a7214cfc0b41d9bb086affc2d8f51c",
    "job_name" : "wgs-gpu-qb4dk",
    "create_time" : "2020-01-15T13:18:52Z"
  } ]
}
```

## 错误码

访问[错误中心](https://error-center.aliyun.com/status/product/CS)查看更多错误码。

## 开发者资源

-   [SDK](https://next.api.aliyun.com/api-tools/sdk/CS?version=2015-12-15&)

    阿里云为您提供多种语言的SDK，帮助您快速通过API集成阿里云的产品和服务，推荐您使用SDK调用API，已免除您手动签名验证。

-   [OpenAPI Explorer](https://next.api.aliyun.com/api/CS/2015-12-15/DescribeWorkflows)

    快速检索，可视化调试API，在线命令行工具，同步动态生成可执行的SDK代码示例。

-   [阿里云CLI](https://github.com/aliyun/aliyun-cli)

    阿里云资产管理和配置工具，可通过命令方式同时管理多个阿里云产品和服务，简单快捷，是您上云好帮手。


