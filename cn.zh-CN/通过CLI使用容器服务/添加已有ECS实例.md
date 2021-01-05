# 添加已有ECS实例

添加已有ECS实例到集群。具体的API描述。

具体的API描述，请参见[添加已有实例到集群](/cn.zh-CN/API参考/节点/添加已有实例到集群.md)。

## API请求响应

请求格式

```
aliyun cs  POST /clusters/<cluster_id>/attach --header "Content-Type=application/json" --body "$(cat attach.json)"
```

参数说明：

-   `--header`需要指定Content-Type为application/json。
-   `--body`是要发送给服务端的body内容，可以从本地文件读取，需要是有效的JSON格式。`attach.json`的内容如下所示。

```
{
    "password": "ECS实例root登录密码",
    "instances": "要添加的实例数组",
    "format_disk": "是否格式化数据盘",
    "key_pair": "密钥对",
    "tags": "给节点打tag标签, 数组格式对象"
}
```

响应结果

```
{
    "list": [
        {
            "code": "200",
            "instanceId": "i-2zee3oiwcyoz7kwd****",
            "message": "successful"
        },
        {
            "code": "200",
            "instanceId": "i-2ze0lgm3y6iylcbt****",
            "message": "successful"
        }
    ],
    "task_id": "T-5a544aff80282e39ea000039"
}
```

