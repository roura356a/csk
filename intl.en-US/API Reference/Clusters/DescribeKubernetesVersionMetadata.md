# DescribeKubernetesVersionMetadata

Queries the details of Kubernetes versions that are supported by Container Service for Kubernetes \(ACK\).

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DescribeKubernetesVersionMetadata&type=ROA&version=2015-12-15)

## Request syntax

```
GET /api/v1/metadata/versions?Region=String&ClusterType=String&KubernetesVersion=String&Profile=String HTTP/1.1 
Content-Type:application/json
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|Region|String|Yes|cn-beijing|The ID of the region where ACK clusters deployed. |
|ClusterType|String|Yes|Kubernetes|The type of ACK clusters. Valid values:

 -   `Kubernetes`: dedicated Kubernetes cluster
-   `ManagedKubernetes`: managed Kubernetes cluster
-   `Ask`: serverless Kubernetes \(ASK\) cluster
-   `ExternalKubernetes`: registered external Kubernetes cluster |
|KubernetesVersion|String|No|1.16.9-aliyun.1|The Kubernetes version that you want to query. You must specify an open source Kubernetes version. We recommend that you specify the latest version. If you do not specify a Kubernetes version, the latest Kubernetes version is queried.

 You can create ACK clusters of the latest two Kubernetes versions in the ACK console. You can also create ACK clusters of other Kubernetes versions by calling the API. For more information about the Kubernetes versions supported by ACK, see [Release notes for Kubernetes versions](~~185269~~). |
|Profile|String|No|Default|The scenario where the ACK cluster is used. Valid values:

 -   `Default`: The ACK cluster is used in non-edge computing scenarios.
-   `Edge`: The ACK cluster is used in edge computing scenarios.

 Default value: `Default`. |

## Response syntax

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

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
| |Array| |The details of the queried Kubernetes version. |
|capabilities|Map| |The features of the queried Kubernetes version. |
|images|Array| |The list of the system images supported by the queried Kubernetes version. |
|image\_id|String|centos\_7\_7\_x64\_20G\_alibase\_20200426.vhd|The ID of the image. |
|image\_name|String|CentOS 7.7|The name of the image. |
|platform|String|CentOS|The operating system. Valid values:

 -   `AliyunLinux`
-   `CentOS`
-   `Windows`
-   `WindowsCore` |
|os\_version|String|7.7|The version of the image. |
|image\_type|String|centos\_7\_7\_20|The type of the image. Examples:

 -   `centos_7_7_20`
-   `aliyunlinux_2_64_20` |
|os\_type|String|Linux|The type of the operating system. Examples:

 -   `Windows`
-   `Linux` |
|image\_category|String|system|The category of the image. Examples:

 -   `system`: public images
-   `self`: custom images
-   `others`: shared images from other Alibaba Cloud accounts
-   `marketplace`: images from the image market |
|meta\_data|Map| |The metadata of the Kubernetes version. |
|runtimes|Array of [runtime](/intl.en-US/API Reference/Commonly used parameters.md)| |The details of the supported container runtimes. |
|version|String|1.16.9-aliyun.1|The Kubernetes version supported by ACK. For more information, see [Release notes for Kubernetes versions](~~185269~~). |
|multi\_az|String|false|Indicates whether the ACK cluster is a multi-zone cluster.

 -   `true`: The ACK cluster is a multi-zone cluster.
-   `false`: The ACK cluster is not a multi-zone cluster. |

## Examples

Sample requests

```
GET /api/v1/metadata/versions?Region=cn-beijing&ClusterType=Kubernetes&KubernetesVersion=1.16.9-aliyun.1&Profile=Default HTTP/1.1 
Content-Type:application/json
```

Sample success responses

`XML` format

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

`JSON` format

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

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

