# 查看资源Tag列表

调用ListTagResources查询可见的资源标签关系。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=CS&api=ListTagResources&type=ROA&version=2015-12-15)

## 请求头

该接口使用公共请求头，无特殊请求头。请参见公共请求参数文档。

## 请求语法

```
get /tags http|https
```

## 请求参数

|名称|类型|是否必选|示例值|描述|
|--|--|----|---|--|
|next\_token|String|否|\*\*\*|下一个查询开始的Token。 |
|resource\_ids|String|是|\["xxxxx","xxxxxx"\]|要查询的集群ID列表。 |
|tags|String|否|\[\{\\"key\\":\\"env\\",\\"value\\",\\"dev\\"\},\{\\"key\\":\\"dev\\", \\"value\\":\\"IT\\"\}\]|要查询的tag列表，采用JSON字符串的形式，最多包含20个子项限制。 |
|resource\_type|String|是|SLB|资源类型定义。 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|next\_token|String|"\*\*\*"|下一个查询开始的Token。 |
|request\_id|String|"E368C761-F8F6-4A36-9B58-BD53D5CD0CEB"|请求ID。 |
|tag\_resources|Object| |Tag资源集。 |
|tag\_resource|Object| |标签资源。 |
|resource\_id|String|"c23421cfa74454bc8b37163fd19af\*\*\*\*"|资源ID。 |
|resource\_type|String|"ALIYUN::CS::CLUSTER"|资源类型。 |
|tag\_key|String|"ack.aliyun.com"|标签key。 |
|tag\_value|String|"c23421cfa74454bc8b37163fd19af\*\*\*\*"|标签值。 |

## 示例

请求示例

```
GET /tags HTTP/1.1
公共请求头
{
    "resource_type":"CLUSTER",
    "resource_ids":[
        "xxx",
        "yyy"
    ],
    "tags":[
        {
            "key":"env",
            "value":"dev"
        },
        {
            "key":"dep",
            "value":"IT"
        }
    ]
}
```

正常返回示例

`XML` 格式

```
<next_token/>
<request_id>E368C761-F8F6-4A36-9B58-BD53D5CD0CEB</request_id>
<tag_resources>
    <tag_resource>
        <tag_key>ack.aliyun.com</tag_key>
        <tag_value>c23421cfa74454bc8b37163fd19af****</tag_value>
        <resource_id>c23421cfa74454bc8b37163fd19af****</resource_id>
        <resource_type>ALIYUN::CS::CLUSTER</resource_type>
    </tag_resource>
    <tag_resource>
        <tag_key>acs:rm:rgId</tag_key>
        <tag_value>rg-acfmyvw3wjm****</tag_value>
        <resource_id>c23421cfa74454bc8b37163fd19af****</resource_id>
        <resource_type>ALIYUN::CS::CLUSTER</resource_type>
    </tag_resource>
</tag_resources>
```

`JSON` 格式

```
{
    "next_token":"",
    "request_id":"E368C761-F8F6-4A36-9B58-BD53D5CD0CEB",
    "tag_resources":{
        "tag_resource":[
            {
                "tag_key":"ack.aliyun.com",
                "tag_value":"c23421cfa74454bc8b37163fd19af****",
                "resource_id":"c23421cfa74454bc8b37163fd19af****",
                "resource_type":"ALIYUN::CS::CLUSTER"
            },
            {
                "tag_key":"acs:rm:rgId",
                "tag_value":"rg-acfmyvw3wjm****",
                "resource_id":"c23421cfa74454bc8b37163fd19af****",
                "resource_type":"ALIYUN::CS::CLUSTER"
            }
        ]
    }
}
```

## 错误码

访问[错误中心](https://error-center.alibabacloud.com/status/product/CS)查看更多错误码。

