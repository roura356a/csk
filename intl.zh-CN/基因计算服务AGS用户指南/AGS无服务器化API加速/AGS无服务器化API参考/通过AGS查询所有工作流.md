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

访问[错误中心](https://error-center.alibabacloud.com/status/product/CS)查看更多错误码。

