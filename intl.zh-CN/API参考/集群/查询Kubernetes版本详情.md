# 查询Kubernetes版本详情

调用DescribeKubernetesVersionMetadata查询容器服务ACK支持的Kubernetes版本的详细信息。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=CS&api=DescribeKubernetesVersionMetadata&type=ROA&version=2015-12-15)

## 请求头

该接口使用公共请求头，无特殊请求头。更多信息，请参见[公共请求参数](~~167755~~)。

## 请求语法

```
GET /api/v1/metadata/versions 
```

## 请求参数

|名称|类型|位置|是否必选|示例值|描述|
|--|--|--|----|---|--|
|Region|String|Query|是|cn-beijing|集群所在地域ID。 |
|ClusterType|String|Query|是|Kubernetes|集群类型，取值：

 -   `Kubernetes`: 专有版集群。
-   `ManagedKubernetes`：托管版集群。
-   `Ask`：Serverless集群。
-   `ExternalKubernetes`：注册集群。 |
|KubernetesVersion|String|Query|否|1.16.9-aliyun.1|集群版本，与Kubernetes社区基线版本保持一致。建议选择最新版本，若不指定，默认使用最新版本。

 目前在ACK控制台您可以创建两种最新版本的集群。您可以通过API创建其他Kubernetes版本集群。ACK支持的Kubernetes版本，请参见[Kubernetes版本发布概览](~~185269~~)。 |
|Profile|String|Query|否|Default|面向场景时的集群类型，取值：

 -   `Default`：非边缘场景集群。
-   `Edge`：边缘场景集群。

 默认值：`Default`。 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
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
|runtimes|Array of [runtime](t.md#)| |容器运行时配置。 |
|version|String|1.16.9-aliyun.1|ACK发布的Kubernetes版本。更多版本信息，请参见[Kubernetes版本发布概览](~~185269~~)。 |
|multi\_az|String|false|是否为多可用区：

 -   `true`：是多可用区。
-   `false`：非多可用区。 |

## 示例

请求示例

```
GET /api/v1/metadata/versions?KubernetesVersion=1.16.9-aliyun.1&Region=cn-beijing&ClusterType=Kubernetes
```

正常返回示例

`XML` 格式

```
<0>
    <version>1.18.8-aliyun.1</version>
    <capabilities>
        <CloudMonitorVersion>1.3.7</CloudMonitorVersion>
        <DockerVersion>17.06.2-ce-3</DockerVersion>
        <EnterpriseSecurityGroup>true</EnterpriseSecurityGroup>
        <EtcdVersion>v3.3.8</EtcdVersion>
        <HpcCluster>true</HpcCluster>
        <IsEnterpriseSecurityGroup>false</IsEnterpriseSecurityGroup>
    </capabilities>
    <multi_az>false</multi_az>
    <meta_data>
        <KubernetesVersion>1.18.8-aliyun.1</KubernetesVersion>
        <DockerVersion>19.03.5</DockerVersion>
        <EtcdVersion>v3.4.3</EtcdVersion>
    </meta_data>
    <runtimes>
        <name>docker</name>
        <version>19.03.5</version>
    </runtimes>
    <runtimes>
        <name>Sandboxed-Container.runv</name>
        <version>2.1.0</version>
    </runtimes>
    <images>
        <image_id>centos_7_7_x64_20G_alibase_20200426.vhd</image_id>
        <image_name>CentOS 7.7</image_name>
        <image_type>centos_7_7_20</image_type>
        <image_category>system</image_category>
        <platform>CentOS</platform>
        <os_type>Linux</os_type>
        <os_version>7.7</os_version>
    </images>
    <images>
        <image_id>aliyun_2_1903_x64_20G_alibase_20200904.vhd</image_id>
        <image_name>Alibaba Cloud Linux 2.1903</image_name>
        <image_type>aliyunlinux_2_64_20</image_type>
        <image_category>system</image_category>
        <platform>AliyunLinux</platform>
        <os_type>Linux</os_type>
        <os_version>2.1903</os_version>
    </images>
</0>
```

`JSON` 格式

```
[
  {
    "version": "1.18.8-aliyun.1",
    "capabilities": {
      "CloudMonitorVersion": "1.3.7",
      "DockerVersion": "17.06.2-ce-3",
      "EnterpriseSecurityGroup": true,
      "EtcdVersion": "v3.3.8",
      "HpcCluster": true,
      "IsEnterpriseSecurityGroup": false
    },
    "multi_az": false,
    "meta_data": {
      "KubernetesVersion": "1.18.8-aliyun.1",
      "DockerVersion": "19.03.5",
      "EtcdVersion": "v3.4.3"
    },
    "runtimes": [
      {
        "name": "docker",
        "version": "19.03.5"
      },
      {
        "name": "Sandboxed-Container.runv",
        "version": "2.1.0"
      }
    ],
    "images": [
      {
        "image_id": "centos_7_7_x64_20G_alibase_20200426.vhd",
        "image_name": "CentOS 7.7",
        "image_type": "centos_7_7_20",
        "image_category": "system",
        "platform": "CentOS",
        "os_type": "Linux",
        "os_version": "7.7"
      },
      {
        "image_id": "aliyun_2_1903_x64_20G_alibase_20200904.vhd",
        "image_name": "Alibaba Cloud Linux 2.1903",
        "image_type": "aliyunlinux_2_64_20",
        "image_category": "system",
        "platform": "AliyunLinux",
        "os_type": "Linux",
        "os_version": "2.1903"
      }
    ]
  }
]
```

## 错误码

访问[错误中心](https://error-center.alibabacloud.com/status/product/CS)查看更多错误码。

