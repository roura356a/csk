# Add existing ENS instances to a managed edge cluster

You can call AttachInstances to add existing Edge Node Service \(ENS\) instances to a managed edge cluster.

## Request information

```
POST /clusters/{cluster_id}/attach HTTP/1.1
```

|Parameter|Type|Required|Description|
|:--------|:---|:-------|:----------|
|cluster\_id|string|Yes|The ID of the cluster.|

Custom request headers

No custom request headers are used. For more information, see [Common parameters](/intl.en-US/API Reference/Common parameters.md) .

Request body

```
{
    "instances":"A list of the ENS instances that you want to add.",
    "is_edge_worker":"Specifies whether to configure the worker node as an edge node.",
}
```

|Parameter|Type|Required|Description|
|:--------|:---|:-------|:----------|
|instances|Array|Yes|A list of the ENS instances that you want to add.|
|is\_edge\_worker|bool|Yes|Specifies whether to configure the worker node as an edge node. Set the value to true.|

## Response information

Response line

```
HTTP/1.1 202 OK
```

Custom response headers

No custom response headers are used. For more information, see [Common parameters](/intl.en-US/API Reference/Common parameters.md) .

Response body

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

## Examples

Sample requests

```
POST /clusters/Cccfd68c474454665ace07efce924****/attach HTTP/1.1
<Common request header>
{
    "is_edge_worker": true
    "instances": [
        "i-xxxx",
        "i-yyyy"
    ]
}
```

Sample success responses

```
HTTP/1.1 202 Accepted
<Common response header>
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

