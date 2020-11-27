# Use AGS to cancel a running workflow

You can call CancelWorkflow to cancel a running workflow.

## Request

Request line

```
PUT /gs/workflow/{workflowName}        HTTP/1.1
```

Custom request headers

None. For more information, see [Common parameters](/intl.en-US/API Reference/Common parameters.md).

Request body

```
    {
        "action": "cancel"
    }
```

|Parameter|Type|Description|
|:--------|:---|-----------|
|action|String|The operation that you want to perform. Valid value: cancel.|

## Response

Response line

```
HTTP/1.1 200 OK
```

Custom response headers

None. For more information, see [Common parameters](/intl.en-US/API Reference/Common parameters.md).

Response body

None.

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
request.set_method('PUT')
request.set_protocol_type('https') # https | http
request.set_domain('cs.cn-beijing.aliyuncs.com')
request.set_version('2015-12-15')

request.add_query_param('RegionId', "cn-beijing")
request.add_header('Content-Type', 'application/json')
request.set_uri_pattern('/gs/workflow/wgs-gpu-97xfn')
body = '''{"action": "cancel"}'''
request.set_content(body.encode('utf-8'))

response = client.do_action_with_exception(request)

print(response)
```

