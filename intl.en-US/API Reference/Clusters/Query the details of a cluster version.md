# Query the details of a cluster version

You can call DescribeKubernetesVersionMetadata to query the details of a cluster version.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DescribeKubernetesVersionMetadata&type=ROA&version=2015-12-15)

## Request parameter types

This operation uses only common request parameters. For more information, see Common parameters.

## Request syntax

```
get /api/v1/metadata/versions http|https
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|Region|String|Yes|cn-beijing|The ID of the region. |
|MultiAZ|Boolean|No|false|Specifies whether to query a multi-zone cluster. |
|ClusterType|String|Yes|Kubernetest|The type of the cluster. For more information about the cluster types, see [Create a cluster](～～167759～～). |
|KubernetesVersion|String|No|1.16.9-aliyun.1|The version of the cluster. For example, 1.16.9-aliyun.1. |
|Profile|String|No|Default|The identifier of an edge cluster.

 -   Default: The cluster is not an edge cluster.
-   Edge: The cluster is an edge cluster. |

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
| |Array of data| |The returned data. |
|capabilities|Object| |The features of the cluster version. |
|images|Array| |The system images of the Elastic Compute Service \(ECS\) instances. |
|image\_id|String|"centos\_7\_7\_x64\_20G\_alibase\_20200426.vhd"|The ID of the image. |
|image\_name|String|"CentOS 7.7"|The name of the image. |
|image\_type|String|"centos\_7\_7\_2"|The type of the image. |
|os\_type|String|"Linux"|The type of the operating system. Valid values: Windows and Linux. Default value: Linux. |
|os\_version|String|"7.7"|The version number of the operating system. |
|platform|String|"CentOS"|The release version of the operating system. Valid values: CentOS, AliyunLinux, Windows, and WindowsCore. |
|meta\_data|Object| |The metadata of the cluster version. |
|multi\_az|String|"false"|Indicates whether the cluster is a multi-zone cluster. |
|runtimes|Array| |The configuration of the container runtime. |
|name|String|"docker"|The name of the container runtime. |
|version|String|"19.05.3"|The version of the container runtime. |
|version|String|"1.16.9-aliyun.1"|The version of the cluster. |

## Examples

Sample requests

```
GET /api/v1/metadata/versions HTTP/1.1
{
    "Region":"cn-beijing",
    "ClusterType":"Kubernetes",
    "MultiAZ":false,
    "KubernetestVersion":"1.16.9-aliyun.1"
}
```

Sample success responses

`XML` format

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

`JSON` format

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

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

