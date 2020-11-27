# Use AGS to query all workflows

You can call DescribeWorkflows to query all workflows.

## Request

Request line

```
GET /gs/workflows  HTTP/1.1
```

Custom request headers

None. For more information, see [Common parameters](/intl.en-US/API Reference/Common parameters.md).

Request body

None.

## Response

Response line

```
HTTP/1.1 200 OK
```

Custom response headers

None. For more information, see [Common parameters](/intl.en-US/API Reference/Common parameters.md).

Response body

```
{
  "jobs": [
    {
      "create_time": "2020-01-15T14:13:16Z",
      "cluster_id": "cb1a7214cfc0b41d9bb086affc2d8f51c",
      "job_name": "mapping-gpu-mhhgh"
    },
    {
      "create_time": "2020-01-15T13:19:26Z",
      "cluster_id": "cb1a7214cfc0b41d9bb086affc2d8f51c",
      "job_name": "mapping-gpu-98wt4"
    },
    {
      "create_time": "2020-01-15T13:18:52Z",
      "cluster_id": "cb1a7214cfc0b41d9bb086affc2d8f51c",
      "job_name": "wgs-gpu-qb4dk"
    }
  ]
}
```

|Parameter|Type|Description|
|:--------|:---|-----------|
|cluster\_id|String|The ID of the cluster.|
|job\_name|String|The name of the workflow.|
|create\_time|String|The time when the workflow is created.|

## Examples

Sample requests \(Python\)

```
#! /usr/bin/env python
#coding=utf-8

from aliyunsdkcore.client import AcsClient
from aliyunsdkcore.request import CommonRequest
import os
client = AcsClient(os.environ['accessKeyID'], os.environ['accessKeySecret'], 'cn-beijing')

request = CommonRequest()
request.set_accept_format('json')
request.set_method('GET')
request.set_protocol_type('https') # https | http
request.set_domain('cs.cn-beijing.aliyuncs.com')
request.set_version('2015-12-15')

request.add_query_param('RegionId', "cn-beijing")
request.add_header('Content-Type', 'application/json')
request.set_uri_pattern('/gs/workflows')

response = client.do_action_with_exception(request)

print(response)
```

