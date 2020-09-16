# 添加已有ENS节点至Kubernetes边缘托管集群

调用AttachInstances接口添加已有ENS节点至边缘托管集群。

## 请求信息

```
POST /clusters/{cluster_id}/attach HTTP/1.1
```

|名称|类型|是否必须|描述|
|:-|:-|:---|:-|
|cluster\_id|string|是|集群ID。|

特有请求头RequestHead

无，请参见[公共请求和返回结果](/intl.zh-CN/API参考/公共请求和返回结果.md)。

请求体RequestBody

```
{
    "instances":[
        "i-wz9e8pvnjalxkggk****"
    ],
    "format_disk":true,
    "rds_instances":[
        "rds-xxx"
    ],
    "keep_instance_name":true,
    "is_edge_worker":true
}
```

|名称|类型|是否必须|描述|
|:-|:-|:---|:-|
|instances|Array|是|已有实例的数组。|
|is\_edge\_worker|bool|是|是否为边缘节点。接入ENS节点时需要配置为true。|
|rds\_instances|Array|否|RDS实例列表。|
|keep\_instance\_name|bool|否|是否保留实例名称。|
|format\_disk|bool|否|是否格式化数据盘。|

## 返回信息

返回行ResponseLine

```
HTTP/1.1 202 OK
```

特有返回头ResponseHead

无，请参见[公共请求和返回结果](/intl.zh-CN/API参考/公共请求和返回结果.md)。

返回体ResponseBody

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

## 示例

请求示例

```
POST /clusters/Cccfd68c474454665ace07efce924****/attach HTTP/1.1
<公共请求头>
{
    "is_edge_worker": true
    "instances": [
        "i-xxxx",
        "i-yyyy"
    ],
    "keep_instance_name":true
}
```

返回示例

```
HTTP/1.1 202 Accepted
<公共响应头>
{
  "list": [
    {
      "code": "200",
      "instanceId": "i-xxxx",
      "message": "successful"
    },
    {
      "code": "200",
      "instanceId": "i-yyyy",
      "message": "successful"
    }
  ],
  "task_id": "T-5d6f733d9e408ec74f000002"
}
```

