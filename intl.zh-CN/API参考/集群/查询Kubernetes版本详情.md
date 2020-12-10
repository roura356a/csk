# 查询Kubernetes版本详情

调用DescribeKubernetesVersionMetadata查询支持的Kubernetes版本的详细信息。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=CS&api=DescribeKubernetesVersionMetadata&type=ROA&version=2015-12-15)

## 请求头

该接口使用公共请求头，无特殊请求头。请参见公共请求参数文档。

## 请求语法

```
GET /api/v1/metadata/versions 
```

## 请求参数

|名称|类型|位置|是否必选|示例值|描述|
|--|--|--|----|---|--|
|Region|String|Query|是|cn-beijing|地域ID。 |
|MultiAZ|Boolean|Query|否|false|是否查询多可用区。 |
|ClusterType|String|Query|是|Kubernetes|集群类型。

 -   Kubernetes: 专有版集群。
-   ManagedKubernetes：托管版集群。
-   Ask：Serverless集群。
-   ExternalKubernetes：注册集群。
-   ServiceMesh：ASM集群。 |
|KubernetesVersion|String|Query|否|1.16.9-aliyun.1|版本号。关于ACK支持的版本号信息，请参见[Kubernetes版本发布概览](~~185269~~)。 |
|Profile|String|Query|否|Default|面向场景时的集群类型：

 -   Default：非边缘场景集群。
-   Edge：边缘场景集群。 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
| |Array of data| |版本详情列表。 |
|capabilities|Map| |Kubernetes版本功能特性。 |
|images|Array| |系统镜像列表。 |
|image\_id|String|centos\_7\_7\_x64\_20G\_alibase\_20200426.vhd|镜像ID。 |
|image\_name|String|CentOS 7.7|镜像名称。 |
|image\_type|String|centos\_7\_7\_2|镜像类型。 |
|os\_type|String|Linux|镜像的操作系统类型。取值范围：

 -   Windows
-   Linux |
|os\_version|String|7.7|镜像的操作系统版本。 |
|platform|String|CentOS|操作系统平台。取值：

 -   AliyunLinux
-   CentOS |
|meta\_data|Map| |Kubernetes版本元数据信息。 |
|multi\_az|String|false|是否为多可用区。 |
|runtimes|Array| |容器运行时配置。 |
|name|String|docker|运行时名称。 |
|version|String|19.05.3|运行时版本。 |
|version|String|1.16.9-aliyun.1|ACK发布的Kubernetes版本。 |

## 示例

请求示例

```
GET /api/v1/metadata/versions?Region=cn-beijing&ClusterType=Kubernetes
公共请求头
```

正常返回示例

`XML` 格式

```
<0>
    <version>1.14.8-aliyun.1</version>
    <multi_az>false</multi_az>
    <capabilities>
        <VpcCidr>192.168.0.0/16</VpcCidr>
        <ZoneId/>
    </capabilities>
    <meta_data>
        <KubernetesVersion>1.14.8-aliyun.1</KubernetesVersion>
    </meta_data>
    <runtimes>
        <name>docker</name>
        <version>19.03.5</version>
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
</0>
```

`JSON` 格式

```
[
    {
        "version":"1.14.8-aliyun.1",
        "multi_az":false,
        "capabilities":{
            "VpcCidr":"192.168.0.0/16",
            "ZoneId":""
        },
        "meta_data":{
            "KubernetesVersion":"1.14.8-aliyun.1"
        },
        "runtimes":[
            {
                "name":"docker",
                "version":"19.03.5"
            }
        ],
        "images":[
            {
                "image_id":"centos_7_7_x64_20G_alibase_20200426.vhd",
                "image_name":"CentOS 7.7",
                "image_type":"centos_7_7_20",
                "image_category":"system",
                "platform":"CentOS",
                "os_type":"Linux",
                "os_version":"7.7"
            }
        ]
    }
]
```

## 错误码

访问[错误中心](https://error-center.alibabacloud.com/status/product/CS)查看更多错误码。

