# DescribeTaskInfo

Queries detailed information about a specific task by ID.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DescribeTaskInfo&type=ROA&version=2015-12-15)

## Request syntax

```
GET /tasks/task_id HTTP/1.1 
Content-Type:application/json
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|task\_id|String|Yes|T-5faa48fb31b6b8078d00\*\*\*\*|The ID of the task. |

## Response syntax

```
HTTP/1.1 200
Content-Type:application/json
{
  "cluster_id" : "String",
  "task_id" : "String",
  "created" : "String",
  "updated" : "String",
  "state" : "String",
  "task_type" : "String",
  "task_result" : [ {
    "data" : "String",
    "status" : "String"
  } ]
}
```

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|cluster\_id|String|c78592bfe92244365b3c3ad47f1de\*\*\*\*|The ID of the ACK cluster. |
|task\_id|String|T-5faa48fb31b6b8078d000006|The ID of the task. |
|created|String|2020-11-10T16:02:04+08:00|The time when the task was created. |
|updated|String|2020-11-10T16:03:06+08:00|The time when the task was updated. |
|state|String|running|The state of the task. Valid values:

 -   `running`: The task is running.
-   `failed`: The task failed.
-   `success`: The task is complete. |
|task\_type|String|cluster\_scaleout|The task type. A value of `cluster_scaleout` indicates a scale-out task. |
|task\_result|Array of task| |The execution result of the task. |
|data|String|i-xxx|The resources that are managed by the task. For a scale-out task, the value of this parameter the ID of the instance that is added by the task. |
|status|String|success|The state of the scaling of the resource. Valid values:

 -   `success`: The scale-out task is successful.
-   `success`: The scale-out task failed.
-   `initail`: The scale-out task is initializing. |

## Examples

Sample requests

```
GET /tasks/T-5faa48fb31b6b8078d00**** HTTP/1.1 
Content-Type:application/json
```

Sample success responses

`XML` format

```
HTTP/1.1 200 OK
Content-Type:application/xml

<cluster_id>c78592bfe92244365b3c3ad47f1de****</cluster_id>
<task_id>T-5faa48fb31b6b8078d000006</task_id>
<created>2020-11-10T16:02:04+08:00</created>
<updated>2020-11-10T16:03:06+08:00</updated>
<state>running</state>
<task_type>cluster_scaleout</task_type>
<task_result>
    <data>i-xxx</data>
    <status>success</status>
</task_result>
```

`JSON` format

```
HTTP/1.1 200 OK
Content-Type:application/json

{
  "cluster_id" : "c78592bfe92244365b3c3ad47f1de****",
  "task_id" : "T-5faa48fb31b6b8078d000006",
  "created" : "2020-11-10T16:02:04+08:00",
  "updated" : "2020-11-10T16:03:06+08:00",
  "state" : "running",
  "task_type" : "cluster_scaleout",
  "task_result" : [ {
    "data" : "i-xxx",
    "status" : "success"
  } ]
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

