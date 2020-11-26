# 查询集群Addons详情

调用DescribeAddons查询集群安装的Addons详情。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=CS&api=DescribeAddons&type=ROA&version=2015-12-15)

## 请求头

该接口使用公共请求头，无特殊请求头。请参见公共请求参数文档。

## 请求语法

```
GET /clusters/components/metadata HTTP|HTTPS
```

## 请求参数

|名称|类型|位置|是否必选|示例值|描述|
|--|--|--|----|---|--|
|region|String|Query|是|cn-beijing|地域ID。 |
|cluster\_type|String|Query|否|kubernetes|集群类型，默认值：kubernetes。 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|ComponentGroups|Array of ComponentGroups| |组件分组列表。 |
|default|Array of String|flexvolume|组件名称。 |
|group\_name|String|storage|分组名称。 |
|items|Array of items| |该分组组件详细信息。 |
|description|String|\*\*\*|组件描述信息。 |
|disabled|Boolean|false|是否禁止默认安装。 |
|name|String|flexvolume|组件名称。 |
|required|String|true|是否必须组件。 |
|version|String|\*\*\*|组件版本。 |
|StandardComponents|Map| |标准组件。 |
| |object| |标准组件的描述信息。 |
|description|String|\*\*\*|该组件是否禁止默认安装。 |
|disabled|Boolean|false|是否禁止默认安装。 |
|name|String|ack-arena|组件名称。 |
|required|String|false|是否为必需组件。 |
|version|String|0.5.0|组件版本。 |

## 示例

请求示例

```
GET /clusters/components/metadata?region=cn-beijing HTTP/1.1
```

正常返回示例

`XML` 格式

```
<StandardComponents>
    <ack-arena>
        <name>ack-arena</name>
        <version>0.5.0</version>
        <disabled>false</disabled>
        <required>false</required>
        <description>***</description>
    </ack-arena>
</StandardComponents>
<ComponentGroups>
    <group_name>storage</group_name>
    <default>flexvolume</default>
    <default>alicloud-disk-controller</default>
    <items>
        <name>flexvolume</name>
        <version>***</version>
        <disabled>false</disabled>
        <required>true</required>
        <description/>
    </items>
</ComponentGroups>
```

`JSON` 格式

```
{
    "StandardComponents":{
        "ack-arena":{
            "name":"ack-arena",
            "version":"0.5.0",
            "disabled":false,
            "required":"false",
            "description":"***"
        }
    },
    "ComponentGroups":[
        {
            "group_name":"storage",
            "default":[
                "flexvolume",
                "alicloud-disk-controller"
            ],
            "items":[
                {
                    "name":"flexvolume",
                    "version":"***",
                    "disabled":false,
                    "required":"true",
                    "description":""
                }
            ]
        }
    ]
}
```

## 错误码

访问[错误中心](https://error-center.aliyun.com/status/product/CS)查看更多错误码。

