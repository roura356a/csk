# Use AGS to create a workflow

You can call StartWorkflow to create a workflow.

## Request

Request line

```
POST /gs/workflow  HTTP/1.1
```

Custom request headers

None. For more information, see [Common parameters](/intl.en-US/API Reference/Common parameters.md).

Request body

```
The following code block shows how to create a mapping workflow:
{
      "workflow_type": "mapping",
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


The following code block shows how to create a whole genome sequencing (WGS) workflow:
{
     "workflow_type": "wgs",
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

|Parameter|Type|Description|
|:--------|:---|-----------|
|workflow\_type|String|The type of the workflow. Valid values: wgs and mapping.|
|service|String|The type of service-level agreement \(SLA\). Valid values: s, g, and p.-   Silver \(S-level\): 1 extra minute is required to process every 1.5 billion base pairs beyond the 90 billion base pairs.
-   Gold \(G-level\): 1 extra minute is required to process every 2 billion base pairs beyond the 90 billion base pairs.
-   Platinum \(P-level\): 1 extra minute is required to process every 3 billion base pairs beyond the 90 billion base pairs. |
|mapping\_oss\_region|String|The region where the Object Storage Service \(OSS\) bucket that stores data of the mapping workflow is deployed.|
|mapping\_fastq\_first\_filename|String|The name of the first FASTQ file of the mapping workflow.|
|mapping\_fastq\_second\_filename|String|The name of the second FASTQ file of the mapping workflow.|
|mapping\_bucket\_name|String|The name of the OSS bucket that stores data of the mapping workflow.|
|mapping\_fastq\_path|String|The path of the FASTQ files of the mapping workflow.|
|mapping\_reference\_path|String|The path of the reference files of the mapping workflow.|
|mapping\_is\_mark\_dup|String|Specifies whether to mark duplicate values.|
|mapping\_bam\_out\_path|String|The output path of the Binary Alignment Map \(BAM\) file.|
|mapping\_bam\_out\_filename|String|The name of the output BAM file.|
|wgs\_oss\_region|String|The region where the OSS bucket that stores data of the WGS workflow is deployed.|
|wgs\_fastq\_first\_filename|String|The name of the first FASTQ file of the WGS workflow.|
|wgs\_fastq\_second\_filename|String|The name of the second FASTQ file of the WGS workflow.|
|wgs\_bucket\_name|String|The name of the OSS bucket that stores data of the WGS workflow.|
|wgs\_fastq\_path|String|The path of the FASTQ files of the WGS workflow.|
|wgs\_reference\_path|String|The path of the reference files of the WGS workflow.|
|wgs\_vcf\_out\_path|String|The path of the Variant Call Format \(VCF\) file.|
|wgs\_vcf\_out\_filename|String|The name of the VCF file.|

## Response

Response line

```
HTTP/1.1 200 OK
```

Custom response headers

None. For more information, see [Common parameters](/intl.en-US/API Reference/Common parameters.md).

Response body

```
The following sample output is returned when a mapping workflow is created. 
{
        JobName: mapping-gpu-66xv7
    }

The following sample output is returned when a WGS workflow is created.
    {
        JobName: wgs-gpu-tvltf
    }
```

|Parameter|Type|Description|
|:--------|:---|-----------|
|JobName|String|The name of the workflow.|

## Examples

Sample requests \(Python\)

```
#! /usr/bin/env python
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

