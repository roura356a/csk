# 开通容器服务ACK

调用OpenAckService接口开通容器服务ACK。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=CS&api=OpenAckService&type=ROA&version=2015-12-15)

## 请求语法

```
POST /service/open?type=String HTTP/1.1 
Content-Type:application/json
```

## 请求参数

|参数名称|类型|是否必选|示例|说明|
|----|--|----|--|--|
|type|String|否|propayasgo|要开通的服务类型。取值：

 -   `propayasgo`：容器服务ACK Pro托管版。
-   `edgepayasgo`：边缘容器服务。
-   `gspayasgo`：基因计算服务。 |

## 响应体语法

```
HTTP/1.1 200
Content-Type:application/json
{
  "request_id" : "String",
  "order_id" : "String"
}
```

## 响应参数

|参数名称|类型|示例|说明|
|----|--|--|--|
|request\_id|String|20758A-585D-4A41-A9B2-28DA8F4F534F|请求ID。 |
|order\_id|String|2067\*\*\*\*\*\*\*0374|开通服务的订单号。 |

## 示例

请求示例

```
POST /service/open?type=propayasgo HTTP/1.1 
Content-Type:application/json
```

正常返回示例

`XML`格式

```
HTTP/1.1 200
Content-Type:application/xml
<request_id>20758A-585D-4A41-A9B2-28DA8F4F534F</request_id>
<order_id>2067*******0374</order_id>
```

`JSON`格式

```
HTTP/1.1 200
Content-Type:application/json
{
  "request_id" : "20758A-585D-4A41-A9B2-28DA8F4F534F",
  "order_id" : "2067*******0374"
}
```

## 错误码

访问[错误中心](https://error-center.aliyun.com/status/product/CS)查看更多错误码。

## 开发者资源

-   [SDK](https://next.api.aliyun.com/api-tools/sdk/CS?version=2015-12-15&)

    阿里云为您提供多种语言的SDK，帮助您快速通过API集成阿里云的产品和服务，推荐您使用SDK调用API，已免除您手动签名验证，详情请参见SDK参考文档链接。

-   [OpenAPI Explorer](https://next.api.aliyun.com/api/CS/2015-12-15/OpenAckService)

    快速检索，可视化调试API，在线命令行工具，同步动态生成可执行的SDK代码示例。

-   [阿里云CLI](https://github.com/aliyun/aliyun-cli)

    阿里云资产管理和配置工具，可通过命令方式同时管理多个阿里云产品和服务，简单快捷，是您上云好帮手。


