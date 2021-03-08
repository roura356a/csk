# 通过AGS取消运行中的工作流

调用CancelWorkflow取消正在运行中的工作流。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=CS&api=CancelWorkflow&type=ROA&version=2015-12-15)

## 请求语法

```
PUT /gs/workflow/workflowName HTTP/1.1
Content-Type:application/json

{
  "action" : "String"
}
```

## 请求参数

|参数名称|类型|是否必选|示例|说明|
|----|--|----|--|--|
|workflowName|String|是|mapping-gpu-mhhgh|工作流名称。 |

|参数名称|类型|是否必选|示例|说明|
|----|--|----|--|--|
|action|String|是|cancel|执行的操作，目前只支持cancel。 |

## 响应体语法

```
HTTP/1.1 200 OK
```

## 响应参数

无响应参数

## 取消运行中的工作流示例

```
PUT /gs/workflow/mapping-gpu-mhhgh HTTP/1.1
Host:cs.aliyuncs.com
Content-Type:application/json

{
  "action" : "cancel"
}
```

正常返回示例

`JSON`格式

```
HTTP/1.1 200 OK
```

## 错误码

访问[错误中心](https://error-center.alibabacloud.com/status/product/CS)查看更多错误码。

