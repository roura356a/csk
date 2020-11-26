# 查看资源Tag列表

调用ListTagResources查询可见的资源标签关系。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=CS&api=ListTagResources&type=ROA&version=2015-12-15)

## 请求头

该接口使用公共请求头，无特殊请求头。请参见公共请求参数文档。

## 请求语法

```
GET /tags 
```

## 请求参数

|名称|类型|位置|是否必选|示例值|描述|
|--|--|--|----|---|--|
|resource\_ids|Array of String|Query|是|c71cf3d796c374bf48644482cb0c3\*\*\*\*|集群ID。 |
|tags|Array|Query|否|\[\{\\"key\\":\\"env\\",\\"value\\",\\"dev\\"\},\{\\"key\\":\\"dev\\", \\"value\\":\\"IT\\"\}\]|要查询的tag列表，限制最多包含20个子项。 |
|key|String|Query|否|ack.aliyun.com|标签key。 |
|value|String|Query|否|c71cf3d796c374bf48644482cb0c3\*\*\*\*|标签值。 |
|next\_token|String|Query|否|\*\*\*|下一个查询开始的Token。 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|next\_token|String|\*\*\*\*\*|下一个查询开始的Token。 |
|request\_id|String|E368C761-F8F6-4A36-9B58-BD53D5CD0CEB|请求ID。 |
|tag\_resources|object| |Tag资源集。 |
|tag\_resource|Array of tag\_resource| |标签资源。 |
|tag\_key|String|ack.aliyun.com|标签key。 |
|tag\_value|String|c71cf3d796c374bf48644482cb0c3\*\*\*\*|标签值。 |
|resource\_id|String|i-xxx|资源ID。 |
|resource\_type|String|CLUSTER|资源类型。 |

## 示例

请求示例

```
GET /tags HTTP/1.1 
公共请求头
{
    "resource_ids":[
        "c71cf3d796c374bf48644482cb0c3****"
    ],
    "next_token":"***",
    "tags":[
        {
            "value":"c71cf3d796c374bf48644482cb0c3****",
            "key":"ack.aliyun.com"
        }
    ]
}
```

正常返回示例

`XML` 格式

```
<tag_resources>
    <tag_resource>
        <tag_value>c71cf3d796c374bf48644482cb0c3****</tag_value>
        <resource_type>CLUSTER</resource_type>
        <resource_id>i-xxx</resource_id>
        <tag_key>ack.aliyun.com</tag_key>
    </tag_resource>
</tag_resources>
<next_token>*****</next_token>
<request_id>E368C761-F8F6-4A36-9B58-BD53D5CD0CEB</request_id>
```

`JSON` 格式

```
{
  "tag_resources": {
    "tag_resource": [
      {
        "tag_value": "c71cf3d796c374bf48644482cb0c3****",
        "resource_type": "CLUSTER",
        "resource_id": "i-xxx",
        "tag_key": "ack.aliyun.com"
      }
    ]
  },
  "next_token": "*****",
  "request_id": "E368C761-F8F6-4A36-9B58-BD53D5CD0CEB"
}
```

## 错误码

访问[错误中心](https://error-center.alibabacloud.com/status/product/CS)查看更多错误码。

