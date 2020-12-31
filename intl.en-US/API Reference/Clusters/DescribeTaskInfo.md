# DescribeTaskInfo

Queries detailed information about a task by the specified task ID.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DescribeTaskInfo&type=ROA&version=2015-12-15)

## Request headers

This operation uses only common request headers. For more information, see the Common request parameters topic.

## Request syntax

```
GET /tasks/{task_id} 
```

## Request parameters

|Parameter|Type|Position|Required|Example|Description|
|---------|----|--------|--------|-------|-----------|
|task\_id|String|Path|Yes|T-5faa48fb31b6b8078d000006|The ID of the task. |

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|cluster\_id|String|c78592bfe92244365b3c3ad47f1de\*\*\*\*|The ID of the ACK cluster. |
|task\_id|String|T-5faa48fb31b6b8078d000006|The ID of the task. |
|created|String|2020-11-10T16:02:04+08:00|The time when the task was created. |
|updated|String|2020-11-10T16:03:06+08:00|The time when the task was updated. |
|state|String|running|The state of the task:

 -   running: The task is running.
-   failed: The task failed.
-   success: The task is completed. |
|task\_type|String|cluster\_scaleout|The task type. A value of `cluster_scaleout` indicates a scale-out task. |
|task\_result|Array of task| |The execution result of the task. |
|data|String|i-xxx|The resources that are managed by the task. For a scale-out task, the value of this parameter is an instance ID because the instance is added by the task. |
|status|String|success|The status of resource scaling:

 -   success: The resources are scaled out.
-   success: The resources fail to be scaled out.
-   initail: The scale-out task is initializing. |

## Examples

Sample requests

```
GET /tasks/T-5faa48fb31b6b8078d000006 HTTP/1.1
Common request header
```

Sample success responses

`XML` format

```
<cluster_id>c78592bfe92244365b3c3ad47f1de93d1</cluster_id>
<created>2020-11-10T16:02:04+08:00</created>
<state>running</state>
<task_id>T-5faa48fb31b6b8078d000006</task_id>
<task_result>
    <data>i-xxxxxx</data>
    <status>success</status>
</task_result>
<task_result>
    <data>i-xxxxxx</data>
    <status>failed</status>
</task_result>
<task_type>cluster_scaleout</task_type>
<updated>2020-11-10T16:03:06+08:00</updated>
```

`JSON` format

```
{
  "cluster_id": "c78592bfe92244365b3c3ad47f1de****",
  "created": "2020-11-10T16:02:04+08:00",
  "state": "running",
  "task_id": "T-5faa48fb31b6b8078d000006",
  "task_result": [
    {
      "data": "i-xxxxxx",
      "status": "success"
    },
    {
      "data": "i-xxxxxx",
      "status": "failed"
    }
  ],
  "task_type": "cluster_scaleout",
  "updated": "2020-11-10T16:03:06+08:00"
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

