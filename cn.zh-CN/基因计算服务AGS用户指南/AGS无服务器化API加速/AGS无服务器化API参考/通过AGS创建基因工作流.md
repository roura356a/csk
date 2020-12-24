# 通过AGS创建基因工作流

调用StartWorkflow创建一个新的基因工作流。

## 请求信息

请求行RequestLine

```
POST /gs/workflow  HTTP/1.1
```

特有请求头RequestHead

无，请参见[公共请求和返回结果](/cn.zh-CN/API参考/公共请求和返回结果.md)。

请求体RequestBody

```
{
      "workflow_type": "mapping",         #这里以mapping为例。
      "service": "s" (#SLA: [s: silver|g: gold|p: platinum])
      "mapping_oss_region": "cn-shenzhen",
      "mapping_fastq_first_filename": "MGISEQ2000_PCR-free_NA12878_1_V100003043_L01_1.fq.gz",
      "mapping_fastq_second_filename": "MGISEQ2000_PCR-free_NA12878_1_V100003043_L01_2.fq.gz",
      "mapping_bucket_name": "gene-shenzhen",
      "mapping_fastq_path": "fastq/MGISEQ2000",
      "mapping_reference_path": "reference/hg19", [Optional]
      "mapping_is_mark_dup": "true",
      "mapping_bam_out_path": "output/bamDirName",
      "mapping_bam_out_filename": "abc.bam"
}


{
     "workflow_type": "wgs",              #这里以WGS为例。
     "service": "s" (#SLA: [s: silver|g: gold|p: platinum]),
     "wgs_oss_region": "cn-shenzhen",
     "wgs_fastq_first_filename": "MGISEQ2000_PCR-free_NA12878_1_V100003043_L01_1.fq.gz",
     "wgs_fastq_second_filename": "MGISEQ2000_PCR-free_NA12878_1_V100003043_L01_2.fq.gz",
     "wgs_bucket_name": "gene-shenzhen",
     "wgs_fastq_path": "fastq/MGISEQ2000",
     "wgs_reference_path": "reference/hg19",
     "wgs_vcf_out_path": "output/vcf",
     "wgs_vcf_out_filename": "abc.vcf"
}
```

|名称|类型|描述|
|:-|:-|--|
|workflow\_type|String|工作流类型，可选值：wgs或mapping。|
|service|String|SLA类型，可选值：s、g、p。-   白银级（s）：超过90 Gbp的部分，按1.5 Gbp/min计算增加的时间。
-   黄金级（g）：超过90 Gbp的部分，按2 Gbp/min计算增加的时间。
-   铂金级（p）：超过90 Gbp的部分，按3 Gbp/min计算增加的时间。 |
|mapping\_oss\_region|String|mapping oss数据的存放region。|
|mapping\_fastq\_first\_filename|String|mapping的第一个fastq文件名。|
|mapping\_fastq\_second\_filename|String|mapping的第二个fastq文件名。|
|mapping\_bucket\_name|String|存放mapping的bucket名称。|
|mapping\_fastq\_path|String|mapping的fastq文件路径。|
|mapping\_reference\_path|String|mapping的reference文件位置。|
|mapping\_is\_mark\_dup|String|是否进行dup。|
|mapping\_bam\_out\_path|String|bam文件输出路径。|
|mapping\_bam\_out\_filename|String|bam文件输出名称。|
|wgs\_oss\_region|String|wgs oss数据的存放region。|
|wgs\_fastq\_first\_filename|String|wgs的第一个fastq文件名。|
|wgs\_fastq\_second\_filename|String|wgs的第二个fastq文件名。|
|wgs\_bucket\_name|String|存放wgs的bucket名称。|
|wgs\_fastq\_path|String|wgs的fastq文件路径。|
|wgs\_reference\_path|String|wgs的reference文件路径。|
|wgs\_vcf\_out\_path|String|wgs的vcf输出路径。|
|wgs\_vcf\_out\_filename|String|wgs的vcf输出文件名称。|

## 返回信息

返回行ResponseLine

```
HTTP/1.1 200 OK
```

特有返回头ResponseHead

无，请参见[公共请求和返回结果](/cn.zh-CN/API参考/公共请求和返回结果.md)。

返回体ResponseBody

```
这里以mapping为例 
{
        JobName: mapping-gpu-66xv7
    }

这里以wgs为例
    {
        JobName: wgs-gpu-tvltf
    }
```

|名称|类型|描述|
|:-|:-|--|
|JobName|String|工作流名称|

## 示例

请求示例（Python）

```
#!/usr/bin/env python
# coding=utf-8

from aliyunsdkcore.client import AcsClient
from aliyunsdkcore.request import CommonRequest
import os

os.environ.setdefault('DEBUG', 'sdk')

client = AcsClient(os.environ['accessKeyID'], os.environ['accessKeySecret'], 'cn-beijing')

request = CommonRequest()
request.set_accept_format('json')
request.set_method('POST')
request.set_protocol_type('https') # https | http
request.set_domain('cs.cn-beijing.aliyuncs.com')
request.set_version('2015-12-15')

request.add_query_param('RegionId', "cn-shenzhen")
request.add_header('Content-Type', 'application/json')
request.set_uri_pattern('/gs/workflow')
body = '''{"cli_version":"v1.0.1-882299b","wgs_bucket_name":"my-test-shenzhen","wgs_fastq_first_name":"MGISEQ/MGISEQ2000_PCR-free_NA12878_1_V100003043_L01_1.fq.gz","wgs_fastq_second_name":"MGISEQ/MGISEQ2000_PCR-free_NA12878_1_V100003043_L01_2.fq.gz","wgs_oss_region":"cn-shenzhen","wgs_reference_file":"hs37d5","wgs_service":"g","wgs_vcf_File_name":"vcf/MGISEQ_NA12878_hs37d5_13.vcf","workflow_type":"WGS"}'''
request.set_content(body.encode('utf-8'))
response = client.do_action_with_exception(request)
```

