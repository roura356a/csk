# 通过AGS创建基因工作流

调用StartWorkflow创建一个新的基因工作流。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=CS&api=StartWorkflow&type=ROA&version=2015-12-15)

## 请求语法

```
POST /gs/workflow HTTP/1.1
Content-Type:application/json

{
  "workflow_type" : "String",
  "service" : "String",
  "mapping_oss_region" : "String",
  "mapping_fastq_first_filename" : "String",
  "mapping_fastq_second_filename" : "String",
  "mapping_bucket_name" : "String",
  "mapping_fastq_path" : "String",
  "mapping_reference_path" : "String",
  "mapping_is_mark_dup" : "String",
  "mapping_bam_out_path" : "String",
  "mapping_bam_out_filename" : "String",
  "wgs_oss_region" : "String",
  "wgs_fastq_first_filename" : "String",
  "wgs_fastq_second_filename" : "String",
  "wgs_bucket_name" : "String",
  "wgs_fastq_path" : "String",
  "wgs_reference_path" : "String",
  "wgs_vcf_out_path" : "String",
  "wgs_vcf_out_filename" : "String"
}
```

## 请求参数

|参数名称|类型|是否必选|示例|说明|
|----|--|----|--|--|
|workflow\_type|String|是|mapping|工作流类型，可选值：wgs或mapping。 |
|service|String|否|s|SLA类型，可选值：s、g、p。

 -   白银级（s）：超过90 Gbp的部分，按1.5 Gbp/min计算增加的时间。
-   黄金级（g）：超过90 Gbp的部分，按2 Gbp/min计算增加的时间。
-   铂金级（p）：超过90 Gbp的部分，按3 Gbp/min计算增加的时间。 |
|mapping\_oss\_region|String|否|cn-hangzhou|mapping oss数据的存放region。 |
|mapping\_fastq\_first\_filename|String|否|MGISEQ2000\_PCR-free\_NA12878\_1\_V100003043\_L01\_1.fq.gz|mapping的第一个fastq文件名。 |
|mapping\_fastq\_second\_filename|String|否|MGISEQ2000\_PCR-free\_NA12878\_1\_V100003043\_L01\_2.fq.gz|mapping的第二个fastq文件名。 |
|mapping\_bucket\_name|String|否|gene-shenzhen|存放mapping的bucket名称。 |
|mapping\_fastq\_path|String|否|fastq/MGISEQ2000|mapping的fastq文件路径。 |
|mapping\_reference\_path|String|否|reference/hg19|mapping的reference文件位置。 |
|mapping\_is\_mark\_dup|String|否|true|是否进行dup。 |
|mapping\_bam\_out\_path|String|否|output/bamDirName|bam文件输出路径。 |
|mapping\_bam\_out\_filename|String|否|abc.bam|bam文件输出名称。 |
|wgs\_oss\_region|String|否|cn-shenzhen|wgs oss数据的存放region。 |
|wgs\_fastq\_first\_filename|String|否|MGISEQ2000\_PCR-free\_NA12878\_1\_V100003043\_L01\_1.fq.gz|wgs的第一个fastq文件名。 |
|wgs\_fastq\_second\_filename|String|否|MGISEQ2000\_PCR-free\_NA12878\_1\_V100003043\_L01\_2.fq.gz|wgs的第二个fastq文件名。 |
|wgs\_bucket\_name|String|否|gene-shenzhen|存放wgs的bucket名称。 |
|wgs\_fastq\_path|String|否|fastq/MGISEQ2000|wgs的fastq文件路径。 |
|wgs\_reference\_path|String|否|reference/hg19|wgs的reference文件路径。 |
|wgs\_vcf\_out\_path|String|否|output/vcf|wgs的vcf输出路径。 |
|wgs\_vcf\_out\_filename|String|否|abc.vcf|wgs的vcf输出文件名称。 |

## 响应体语法

```
HTTP/1.1 200 OK
Content-Type:application/json

{
  "JobName" : "String"
}
```

## 响应参数

|参数名称|类型|示例|说明|
|----|--|--|--|
|JobName|String|mapping-gpu-66xv7|工作流名称 |

## 创建一个 mapping 类型的工作流

```
POST /gs/workflow HTTP/1.1
Host:cs.aliyuncs.com
Content-Type:application/json

{
  "workflow_type" : "mapping",
  "service" : "s",
  "mapping_oss_region" : "cn-hangzhou",
  "mapping_fastq_first_filename" : "MGISEQ2000_PCR-free_NA12878_1_V100003043_L01_1.fq.gz",
  "mapping_fastq_second_filename" : "MGISEQ2000_PCR-free_NA12878_1_V100003043_L01_2.fq.gz",
  "mapping_bucket_name" : "gene-shenzhen",
  "mapping_fastq_path" : "fastq/MGISEQ2000",
  "mapping_reference_path" : "reference/hg19",
  "mapping_is_mark_dup" : "true",
  "mapping_bam_out_path" : "output/bamDirName",
  "mapping_bam_out_filename" : "abc.bam",
  "wgs_oss_region" : "cn-shenzhen",
  "wgs_fastq_first_filename" : "MGISEQ2000_PCR-free_NA12878_1_V100003043_L01_1.fq.gz",
  "wgs_fastq_second_filename" : "MGISEQ2000_PCR-free_NA12878_1_V100003043_L01_2.fq.gz",
  "wgs_bucket_name" : "gene-shenzhen",
  "wgs_fastq_path" : "fastq/MGISEQ2000",
  "wgs_reference_path" : "reference/hg19",
  "wgs_vcf_out_path" : "output/vcf",
  "wgs_vcf_out_filename" : "abc.vcf"
}
```

请求示例补充说明

```
创建一个`wgs`类型的工作流：
```
POST /gs/workflow HTTP/1.1 
Content-Type:application/json
{
  "workflow_type" : "wgs",
  "service" : "s",
  "wgs_oss_region" : "cn-shenzhen",
  "wgs_fastq_first_filename" : "MGISEQ2000_PCR-free_NA12878_1_V100003043_L01_1.fq.gz",
  "wgs_fastq_second_filename" : "MGISEQ2000_PCR-free_NA12878_1_V100003043_L01_2.fq.gz",
  "wgs_bucket_name" : "gene-shenzhen",
  "wgs_fastq_path" : "fastq/MGISEQ2000",
  "wgs_reference_path" : "reference/hg19",
  "wgs_vcf_out_path" : "output/vcf",
  "wgs_vcf_out_filename" : "abc.vcf"
}
```
```

正常返回示例

`XML`格式

```
HTTP/1.1 200 OK
Content-Type:application/xml

<JobName>mapping-gpu-66xv7</JobName>
```

`JSON`格式

```
HTTP/1.1 200 OK
Content-Type:application/json

{
  "JobName" : "mapping-gpu-66xv7"
}
```

## 错误码

访问[错误中心](https://error-center.alibabacloud.com/status/product/CS)查看更多错误码。

