# 通过AGS查询单个工作流

调用DescribeWorkflow查询单个工作流的详细信息。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=CS&api=DescirbeWorkflow&type=ROA&version=2015-12-15)

## 请求语法

```
GET /gs/workflow/workflowName HTTP/1.1
Content-Type:application/json
```

## 请求参数

|参数名称|类型|是否必选|示例|说明|
|----|--|----|--|--|
|workflowName|String|是|mapping-gpu-mhhgh|工作流名称 |

## 响应体语法

```
HTTP/1.1 200 OK
Content-Type:application/json

{
  "create_time" : "String",
  "duration" : "String",
  "finish_time" : "String",
  "input_data_size" : "String",
  "job_name" : "String",
  "job_namespace" : "String",
  "output_data_size" : "String",
  "status" : "String",
  "total_bases" : "String",
  "total_reads" : "String",
  "user_input_data" : "String"
}
```

## 响应参数

|参数名称|类型|示例|说明|
|----|--|--|--|
|create\_time|String|2020-01-15 16:30:25 +0800 CST|工作流创建时间。 |
|duration|String|1h15m33.529968361s|工作流经过时长。 |
|finish\_time|String|0001-01-01 00:00:00 +0000 UTC|任务结束时间。 |
|input\_data\_size|String|0|输入数据大小。 |
|job\_name|String|wgs-gpu-97xfn|工作流名称。 |
|job\_namespace|String|1171330362041663|工作流所在命名空间。 |
|output\_data\_size|String|0|输出数据大小。 |
|status|String|Running|工作流当前状态。 |
|total\_bases|String|0|碱基对个数。 |
|total\_reads|String|0|Reads个数。 |
|user\_input\_data|String|\{\\"wgs\_oss\_region\\":\\"cn-shenzhen\\",\\"wgs\_fastq\_first\_name\\":\\"fastq/huada/MGISEQ-200019SZ0002402\\",\\"wgs\_fastq\_second\_name\\":\\"fastq/huada/MGISEQ-200019SZ0002402\\",\\"wgs\_bucket\_name\\":\\"gene-shenzhen\\",\\"wgs\_vcf\_file\_name\\":\\"output/vcf/huada.vcf\\",\\"wgs\_bam\_file\_name\\":\\"output/bam/huada.bam\\",\\"wgs\_reference\_file\\":\\"hg19\\",\\"wgs\_service\\":\\"g\\"\}|用户输入参数。 |

## 查询单个工作流

```
GET /gs/workflow/mapping-gpu-mhhgh HTTP/1.1
Host:cs.aliyuncs.com
Content-Type:application/json
```

正常返回示例

`XML`格式

```
HTTP/1.1 200 OK
Content-Type:application/xml

<create_time>2020-01-15 16:30:25 +0800 CST</create_time>
<duration>1h15m33.529968361s</duration>
<finish_time>0001-01-01 00:00:00 +0000 UTC</finish_time>
<input_data_size>0</input_data_size>
<job_name>wgs-gpu-97xfn</job_name>
<job_namespace>1171330362041663</job_namespace>
<output_data_size>0</output_data_size>
<status>Running</status>
<total_bases>0</total_bases>
<total_reads>0</total_reads>
<user_input_data>{\"wgs_oss_region\":\"cn-shenzhen\",\"wgs_fastq_first_name\":\"fastq/huada/MGISEQ-200019SZ0002402\",\"wgs_fastq_second_name\":\"fastq/huada/MGISEQ-200019SZ0002402\",\"wgs_bucket_name\":\"gene-shenzhen\",\"wgs_vcf_file_name\":\"output/vcf/huada.vcf\",\"wgs_bam_file_name\":\"output/bam/huada.bam\",\"wgs_reference_file\":\"hg19\",\"wgs_service\":\"g\"}</user_input_data>
```

`JSON`格式

```
HTTP/1.1 200 OK
Content-Type:application/json

{
  "create_time" : "2020-01-15 16:30:25 +0800 CST",
  "duration" : "1h15m33.529968361s",
  "finish_time" : "0001-01-01 00:00:00 +0000 UTC",
  "input_data_size" : "0",
  "job_name" : "wgs-gpu-97xfn",
  "job_namespace" : "1171330362041663",
  "output_data_size" : "0",
  "status" : "Running",
  "total_bases" : "0",
  "total_reads" : "0",
  "user_input_data" : "{\\\"wgs_oss_region\\\":\\\"cn-shenzhen\\\",\\\"wgs_fastq_first_name\\\":\\\"fastq/huada/MGISEQ-200019SZ0002402\\\",\\\"wgs_fastq_second_name\\\":\\\"fastq/huada/MGISEQ-200019SZ0002402\\\",\\\"wgs_bucket_name\\\":\\\"gene-shenzhen\\\",\\\"wgs_vcf_file_name\\\":\\\"output/vcf/huada.vcf\\\",\\\"wgs_bam_file_name\\\":\\\"output/bam/huada.bam\\\",\\\"wgs_reference_file\\\":\\\"hg19\\\",\\\"wgs_service\\\":\\\"g\\\"}"
}
```

## 错误码

访问[错误中心](https://error-center.aliyun.com/status/product/CS)查看更多错误码。

## 开发者资源

-   [SDK](https://next.api.aliyun.com/api-tools/sdk/CS?version=2015-12-15&)

    阿里云为您提供多种语言的SDK，帮助您快速通过API集成阿里云的产品和服务，推荐您使用SDK调用API，已免除您手动签名验证。

-   [OpenAPI Explorer](https://next.api.aliyun.com/api/CS/2015-12-15/DescirbeWorkflow)

    快速检索，可视化调试API，在线命令行工具，同步动态生成可执行的SDK代码示例。

-   [阿里云CLI](https://github.com/aliyun/aliyun-cli)

    阿里云资产管理和配置工具，可通过命令方式同时管理多个阿里云产品和服务，简单快捷，是您上云好帮手。


