# 获取集群kubeconfig接口

调用DescribeClusterUserKubeconfig返回包含当前登录用户身份信息的Kubernetes集群访问kubeconfig。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=CS&api=DescribeClusterUserKubeconfig&type=ROA&version=2015-12-15)

## 请求语法

```
GET /k8s/ClusterId/user_config?PrivateIpAddress=Boolean&TemporaryDurationMinutes=Long HTTP/1.1
Content-Type:application/json
```

## 请求参数

|参数名称|类型|是否必选|示例|说明|
|----|--|----|--|--|
|ClusterId|String|是|c5b5e80b0b64a4bf6939d2d8fbbc5\*\*\*\*|集群ID。 |

|参数名称|类型|是否必选|示例|说明|
|----|--|----|--|--|
|PrivateIpAddress|Boolean|否|true|是否获取内网连接配置。取值：

 -   `ture`：仅获取内网连接凭据。
-   `false`：仅获取公网连接凭据。

 默认值：`false`。 |
|TemporaryDurationMinutes|Long|否|15|临时kubeconfig有效期，单位：分钟。取值范围：15（15分钟）～4320（3天）。

 **说明：** 当不设置该参数时，将由系统自动确定一个更长的有限期，具体过期时间通过返回的`expiration`字段的值确定。 |

## 响应体语法

```
HTTP/1.1 200 OK
Content-Type:application/json

{
  "config" : "String",
  "expiration" : "String"
}
```

## 响应参数

|参数名称|类型|示例|说明|
|----|--|--|--|
|config|String|apiVersion: v1\*\*\*\*|集群访问配置。关于如何查看访问集群配置信息，请参见[配置集群凭据](~~86494~~)。 |
|expiration|String|2024-03-10T09:56:17Z|kubeconfig的过期时间。格式：RFC3339格式的UTC时间。 |

## 获取集群kubeconfig接口示例

请求示例

```
GET /k8s/c5b5e80b0b64a4bf6939d2d8fbbc5****/user_config?PrivateIpAddress=true&TemporaryDurationMinutes=15 HTTP/1.1
Host:cs.aliyuncs.com
Content-Type:application/json
```

正常返回示例

`XML`格式

```
HTTP/1.1 200 OK
Content-Type:application/xml

<config>
apiVersion: v1
clusters:
- cluster:
...
</config>
<expiration>2021-03-25T10:01:20Z</expiration>
```

`JSON`格式

```
HTTP/1.1 200 OK
Content-Type:application/json

{
  "config" : "\napiVersion: v1\nclusters:\n- cluster:\n...\n",
  "expiration" : "2021-03-25T10:01:20Z"
}
```

## 错误码

访问[错误中心](https://error-center.alibabacloud.com/status/product/CS)查看更多错误码。

