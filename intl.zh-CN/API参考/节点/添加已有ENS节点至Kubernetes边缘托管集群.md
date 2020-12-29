# 添加已有ENS节点至Kubernetes边缘托管集群

调用AttachInstances接口添加已有ENS节点至边缘托管集群。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=CS&api=AttachInstances&type=ROA&version=2015-12-15)

## 请求头

该接口使用公共请求头，无特殊请求头。更多信息，请参见[公共请求参数](公共请求参数t1884803.dita#concept_944293)。

## 请求语法

```
POST /clusters/{ClusterId}/attach 
```

## 请求参数

|名称|类型|是否必须|示例值|描述|
|--|--|----|---|--|
|cluster\_id|String|是|Cccfd68c474454665ace07efce924\*\*\*\*|集群ID。|
|instances|Array|是|\["i-5j443uf\*\*\*\*"\]|已有实例的数组。|
|is\_edge\_worker|Boolean|是|true|是否作为边缘Worker节点。取值：-   `true`：作为边缘Worker节点。
-   `false`：不作为边缘Worker节点。

当添加已有ENS节点至Kubernetes边缘托管集群时，只能设置为`true`。 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|list|Array of list| |节点信息列表。|
|code|String|200|结果状态码。|
|instanceId|String|i-5j2zjis\*\*\*\*|节点实例ID。|
|message|String|successful|添加结果描述信息。|
|task\_id|String|T-5a544aff80282e39ea000039|任务ID。|

## 示例

请求示例

```
POST /clusters/Cccfd68c474454665ace07efce924****/attach
<公共请求头>
{
    "instances":[
        "i-wz9e8pvnjalxkggk****"
    ],
    "is_edge_worker":true
}
```

返回示例

```
{
  "list": [
    {
      "code": "200",
      "instanceId": "i-5j2zjis****",
      "message": "successful"
    },
    {
      "code": "200",
      "instanceId": "i-5j443uf****",
      "message": "successful"
    }
  ],
  "task_id": "T-5d6f733d9e408ec74f000002"
}
```

## 错误码

访问[错误中心](https://error-center.alibabacloud.com/status/product/CS)查看更多错误码。

