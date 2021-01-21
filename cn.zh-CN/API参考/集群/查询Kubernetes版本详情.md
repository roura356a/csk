# 查询Kubernetes版本详情

调用DescribeKubernetesVersionMetadata查询容器服务ACK支持的Kubernetes版本的详细信息。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=CS&api=DescribeKubernetesVersionMetadata&type=ROA&version=2015-12-15)

## 请求语法

```
GET /api/v1/metadata/versions?Region=String&ClusterType=String&KubernetesVersion=String&Profile=String HTTP/1.1 
Content-Type:application/json
```

## 请求参数

|参数名称|类型|是否必选|示例|说明|
|----|--|----|--|--|
|Region|String|是|cn-beijing|集群所在地域ID。 |
|ClusterType|String|是|Kubernetes|集群类型，取值：

 -   `Kubernetes`: 专有版集群。
-   `ManagedKubernetes`：托管版集群。
-   `Ask`：Serverless集群。
-   `ExternalKubernetes`：注册集群。 |
|KubernetesVersion|String|否|1.16.9-aliyun.1|集群版本，与Kubernetes社区基线版本保持一致。建议选择最新版本，若不指定，默认使用最新版本。

 目前在ACK控制台您可以创建两种最新版本的集群。您可以通过API创建其他Kubernetes版本集群。ACK支持的Kubernetes版本，请参见[Kubernetes版本发布概览](~~185269~~)。 |
|Profile|String|否|Default|面向场景时的集群类型，取值：

 -   `Default`：非边缘场景集群。
-   `Edge`：边缘场景集群。

 默认值：`Default`。 |

## 响应体语法

```
HTTP/1.1 200
Content-Type:application/json
[ {
  "images" : [ {
    "image_id" : "String",
    "image_name" : "String",
    "platform" : "String",
    "os_version" : "String",
    "image_type" : "String",
    "os_type" : "String",
    "image_category" : "String"
  } ],
  "runtimes" : [ {
    "name" : "String",
    "version" : "String"
  } ],
  "version" : "String",
  "multi_az" : "String"
} ]
```

## 响应参数

|参数名称|类型|示例|说明|
|----|--|--|--|
| |Array| |集群版本详情列表。 |
|capabilities|Map| |Kubernetes版本功能特性。 |
|images|Array| |系统镜像列表。 |
|image\_id|String|centos\_7\_7\_x64\_20G\_alibase\_20200426.vhd|镜像ID。 |
|image\_name|String|CentOS 7.7|镜像名称。 |
|platform|String|CentOS|操作系统平台。取值：

 -   `AliyunLinux`
-   `CentOS`
-   `Windows`
-   `WindowsCore` |
|os\_version|String|7.7|镜像版本。 |
|image\_type|String|centos\_7\_7\_20|镜像类型。例如：

 -   `centos_7_7_20`
-   `aliyunlinux_2_64_20` |
|os\_type|String|Linux|操作系统类型，例如：

 -   `Windows`
-   `Linux` |
|image\_category|String|system|镜像分类，部分取值：

 -   `system`：公共镜像。
-   `self`：您的自定义镜像。
-   `others`：其他用户的公开镜像。
-   `marketplace`：镜像市场镜像。 |
|meta\_data|Map| |Kubernetes版本元数据信息。 |
|runtimes|Array of [runtime](/cn.zh-CN/API参考/通用数据结构.md)| |容器运行时配置。 |
|version|String|1.16.9-aliyun.1|ACK发布的Kubernetes版本。更多版本信息，请参见[Kubernetes版本发布概览](~~185269~~)。 |
|multi\_az|String|false|是否为多可用区：

 -   `true`：是多可用区。
-   `false`：非多可用区。 |

## 查询Kubernetes版本详情示例

请求示例

```
GET /api/v1/metadata/versions?Region=cn-beijing&ClusterType=Kubernetes&KubernetesVersion=1.16.9-aliyun.1&Profile=Default HTTP/1.1 
Content-Type:application/json
```

正常返回示例

`XML`格式

```
HTTP/1.1 200 OK
Content-Type:application/xml

<0>
    <images>
        <image_id>centos_7_7_x64_20G_alibase_20200426.vhd</image_id>
        <image_name>CentOS 7.7</image_name>
        <platform>CentOS</platform>
        <os_version>7.7</os_version>
        <image_type>centos_7_7_20</image_type>
        <os_type>Linux</os_type>
        <image_category>system</image_category>
    </images>
    <runtimes>
        <name>docker</name>
        <version>19.03.5</version>
    </runtimes>
    <version>1.16.9-aliyun.1</version>
    <multi_az>false</multi_az>
</0>
```

`JSON`格式

```
HTTP/1.1 200 OK
Content-Type:application/json

[ {
  "images" : [ {
    "image_id" : "centos_7_7_x64_20G_alibase_20200426.vhd",
    "image_name" : "CentOS 7.7",
    "platform" : "CentOS",
    "os_version" : "7.7",
    "image_type" : "centos_7_7_20",
    "os_type" : "Linux",
    "image_category" : "system"
  } ],
  "runtimes" : [ {
    "name" : "docker",
    "version" : "19.03.5"
  } ],
  "version" : "1.16.9-aliyun.1",
  "multi_az" : "false"
} ]
```

## 错误码

访问[错误中心](https://error-center.aliyun.com/status/product/CS)查看更多错误码。

## 开发者资源

-   [SDK](https://next.api.aliyun.com/api-tools/sdk/CS?version=2015-12-15&)

    阿里云为您提供多种语言的SDK，帮助您快速通过API集成阿里云的产品和服务，推荐您使用SDK调用API，已免除您手动签名验证，详情请参见SDK参考文档链接。

-   [OpenAPI Explorer](https://next.api.aliyun.com/api/CS/2015-12-15/DescribeKubernetesVersionMetadata)

    快速检索，可视化调试API，在线命令行工具，同步动态生成可执行的SDK代码示例。

-   [阿里云CLI](https://github.com/aliyun/aliyun-cli)

    阿里云资产管理和配置工具，可通过命令方式同时管理多个阿里云产品和服务，简单快捷，是您上云好帮手。


