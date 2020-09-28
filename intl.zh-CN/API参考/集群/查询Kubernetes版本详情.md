# 查询Kubernetes版本详情

调用DescribeKubernetesVersionMetadata查询支持的Kubernetes版本的详细信息。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=CS&api=DescribeKubernetesVersionMetadata&type=ROA&version=2015-12-15)

## 请求头

该接口使用公共请求头，无特殊请求头。请参见公共请求参数文档。

## 请求语法

```
get /api/v1/metadata/versions http|https
```

## 请求参数

|名称|类型|是否必选|示例值|描述|
|--|--|----|---|--|
|Region|String|是|cn-beijing|地域ID。 |
|MultiAZ|Boolean|否|false|是否查询多可用区。 |
|ClusterType|String|是|Kubernetest|集群类型，更多集群类型前参见[创建Kubernetes集群](～～167759～～)。 |
|KubernetesVersion|String|否|1.16.9-aliyun.1|Kubernetes版本，例如：1.16.9-aliyun.1。 |
|Profile|String|否|Default|边缘集群标识。

 -   Default：非边缘集群。
-   Edge：边缘集群。 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
| |Array of data| |返回数据体。 |
|capabilities|Object| |Kubernetes版本特性。 |
|images|Array| |ECS系统镜像列表。 |
|image\_id|String|"centos\_7\_7\_x64\_20G\_alibase\_20200426.vhd"|镜像ID。 |
|image\_name|String|"CentOS 7.7"|镜像名称。 |
|image\_type|String|"centos\_7\_7\_2"|镜像类型。 |
|os\_type|String|"Linux"|操作系统发行版本号。 |
|os\_version|String|"7.7"|操作系统平台类型。取值范围： Windows、Linux（默认）。 |
|platform|String|"CentOS"|操作系统发行版。取值范围： CentOS、AliyunLinux、Windows、WindowsCore。 |
|meta\_data|Object| |Kubernetes版本元数据信息。 |
|multi\_az|String|"false"|是否为多可用区。 |
|runtimes|Array| |容器运行时配置。 |
|name|String|"docker"|运行时名称。 |
|version|String|"19.05.3"|运行时版本。 |
|version|String|"1.16.9-aliyun.1"|Kubernetes版本。 |

## 示例

请求示例

```
GET /api/v1/metadata/versions HTTP/1.1
{
    "Region":"cn-beijing",
    "ClusterType":"Kubernetes",
    "MultiAZ":false,
    "KubernetestVersion":"1.16.9-aliyun.1"
}
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

