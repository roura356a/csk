# Use AGS to query the information about a workflow

You can call DescribeWorkflow to query the information about a workflow.

## Request

Request line

```
GET /gs/workflow/{workflowName}  HTTP/1.1
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
  "create_time": "2020-01-15 16:30:25 +0800 CST",
  "duration": "1h15m33.529968361s",
  "finish_time": "0001-01-01 00:00:00 +0000 UTC",
  "input_data_size": "0",
  "job_name": "wgs-gpu-97xfn",
  "job_namespace": "1171330362041663",
  "output_data_size": "0",
  "status": "Running",
  "total_bases": "0",
  "total_reads": "0",
  "user_input_data": "{\"wgs_oss_region\":\"cn-shenzhen\",\"wgs_fastq_first_name\":\"fastq/huada/MGISEQ-200019SZ0002402\",\"wgs_fastq_second_name\":\"fastq/huada/MGISEQ-200019SZ0002402\",\"wgs_bucket_name\":\"gene-shenzhen\",\"wgs_vcf_file_name\":\"output/vcf/huada.vcf\",\"wgs_bam_file_name\":\"output/bam/huada.bam\",\"wgs_reference_file\":\"hg19\",\"wgs_service\":\"g\"}"
}
```

|Parameter|Type|Description|
|:--------|:---|-----------|
|create\_time|String|The time when the workflow was created.|
|duration|String|The duration of the workflow.|
|finish\_time|String|The end of the time period when the workflow runs.|
|input\_data\_size|String|The size of the input data.|
|job\_name|String|The name of the workflow.|
|job\_namespace|String|The namespace to which the workflow belongs.|
|output\_data\_size|String|The size of the output data.|
|status|String|The current status of the workflow.|
|total\_bases|String|The number of base pairs.|
|total\_reads|String|The number of reads.|
|user\_input\_data|String|The user input parameters.|

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
request.set_uri_pattern('/gs/workflow/wgs-gpu-97xfn')

response = client.do_action_with_exception(request)

print(response)
```

