---
keyword: [AGS, genomics compute service, whole genome sequencing, WGS]
---

# Use AGS to perform WGS tasks

Alibaba Cloud Genomics Compute Service \(AGS\) enables rapid processing of whole genome sequencing \(WGS\) tasks, including gene comparison, sequencing, deduplication, and variant detection. This topic describes how to manage WGS workflows by using AGS CLI.

## Prerequisites

You have applied for the [qualification of AGS public preview](https://survey.aliyun.com/apps/zhiliao/k6mkBtyn) and your application is approved.

**Note:** If your account is a Resource Access Management \(RAM\) user, you must also provide the UID of the RAM user in the application. You can log on to the [Account Management console](https://account.console.aliyun.com/?#/secure) to obtain the UID of the RAM user.

## Preparations

Grant permissions and prepare data.

1.  Set up AGS.

    For more information about how to download and install AGS CLI, see [Introduction to AGS CLI](/intl.en-US/User Guide for Genomics Service/AGS workflow/Introduction to AGS CLI.md).

    ```
    ags config init
    ```

2.  Prepare an Object Storage Bucket \(OSS\) bucket and grant AGS the read and write permissions on the bucket and the permissions to call GetBucketInfo.

    **Note:**

    -   Make sure that the OSS bucket that you want to use is owned by the current account. Otherwise, we recommend that you create a new OSS bucket under the account and grant the permissions to call GetBucketInfo.
    -   If your account is a RAM user, you must attach the AliyunOSSFullAccess policy to the RAM user. For more information, see [Grant permissions to a RAM user](/intl.en-US/RAM User Management/Grant permissions to a RAM user.md).
    -   If your account is a RAM user, we recommend that you create a new OSS bucket under the account to ensure that the OSS bucket is owned by the RAM user. You can run the following command to check the owner of the OSS bucket:

        ```
        ossutil stat oss://<your new bucket name>
        ```

    ```
    Usage:
    ags config oss <your bucket name>
    
    e.g.
    ags config oss my-test-shenzhen
    ```

3.  Upload FASTQ data to your OSS bucket by using ossutil.

    For more information about how to download and install ossutil, see [Download and installation](/intl.en-US/Tools/ossutil/Download and installation.md).

    **Note:** AGS supports only WGS and whole exome sequencing \(WES\) of human genome data. The comparisons of methylated genome data, plant genome data, and animal genome data are not supported.

    ```
    Usage:
    
    ossutil cp -r <local dir of fastq> <path of  oss bucket >
    
    e. g.
    ossutil cp -r . /MGISEQ oss://my-test-shenzhen/MGISEQ
    ```


## Start a WGS workflow

We recommend that you use version hs37d5 of human reference genome `hg19`. This is also the default genome.

**Note:** Version hs37d5 of human reference genome `hg19` has the following characteristics:

-   Excludes alternative \(ALT\) contigs.
-   Hard masks pseudoautosomal regions \(PARs\) on the Y chromosome \(chrY\).
-   Includes decoy contigs.

AGS is ALT-aware, which enables AGS to identify and process ALT contigs. Genome `UCSC hg19` includes ALT contigs but `does not` have the other two characteristics, which decreases the accuracy of variant detection. For more information, click [here](http://lh3.github.io/2017/11/13/which-human-reference-genome-to-use).

```
Usage:

ags remote run wgs \
--region cn-shenzhen # region of oss, e.g. cn-shenzhen, cn-beijing and etc\
--fastq1 MGISEQ/MGISEQ2000_PCR-free_NA12878_1_V100003043_L01_1.fq.gz # filename of fastq pair 2, fastq-path\filename \
--fastq2 MGISEQ/MGISEQ2000_PCR-free_NA12878_1_V100003043_L01_2.fq.gz  # filename of fastq pair 1\
--bucket my-test-shenzhen # Bucket name\
--output-bam bam/MGISEQ_NA12878_hs37d5.bam, # Output BAM to bucket,  By default empty, non output of BAM \
--output-vcf vcf/MGISEQ_NA12878_hs37d5_5.vcf # Output filename \
--service "g" #SLA: [n:normal|s:silver|g:gold|p:platinum]\
--reference [hg19|hg38|<reference path on OSS>] # hg19: it is hs37d5 version, GRCh37/hg19 include decoy contig, no support for UCSC hg19. hg38: GRCh38/hg38 include decoy
--reference-group "\"@RG\\tID:TEST\\tSM:12878\\tPL:MGISEQ2000\"" # allow to specify reference groups for PL/SM/ID and etc
e.g.
ags remote run wgs \
--region cn-shenzhen \
--fastq1 MGISEQ/MGISEQ2000_PCR-free_NA12878_1_V100003043_L01_1.fq.gz  \
--fastq2 MGISEQ/MGISEQ2000_PCR-free_NA12878_1_V100003043_L01_2.fq.gz  \
--bucket my-test-shenzhen \
--output-vcf vcf/MGISEQ_NA12878_hs37d5_5.vcf \
--output-bam bam/MGISEQ_NA12878_hs37d5_5.bam \
--service "s" \
--reference hg19

### Process FASTQ files that include multiple lanes and samples in batches
MGISAMPLE001 is a set of WGS sequencing samples of multiple lanes. You can combine and compute the sequencing results of multiple lanes by specifying the sample directory --fastq1 MGISAMPLE001 or --fastq2 MGISAMPLE001.
oss://my-test-shenzhen/MGISAMPLE001/L1/MGISEQ2000_PCR-free_NA12878_1_V100003043_L01_1.fq.gz
oss://my-test-shenzhen/MGISAMPLE001/L2/MGISEQ2000_PCR-free_NA12878_1_V100003043_L02_1.fq.gz
oss://my-test-shenzhen/MGISAMPLE001/L1/MGISEQ2000_PCR-free_NA12878_1_V100003043_L01_2.fq.gz
oss://my-test-shenzhen/MGISAMPLE001/L2/MGISEQ2000_PCR-free_NA12878_1_V100003043_L02_2.fq.gz

ags remote run wgs \
--region cn-shenzhen \
--fastq1 MGISAMPLE001 \
--fastq2 MGISAMPLE001  \
--bucket my-test-shenzhen \
--output-vcf vcf/MGISEQ_NA12878_hs37d5_6.vcf \
--output-bam bam/MGISEQ_NA12878_hs37d5_6.bam \
--service "g" \
--reference hg19

ags remote run wgs \
--region cn-shenzhen \
--fastq1 MGISAMPLE002 \
--fastq2 MGISAMPLE002  \
--bucket my-test-shenzhen \
--output-vcf vcf/MGISEQ_NA12878_hs37d5_7.vcf \
--output-bam bam/MGISEQ_NA12878_hs37d5_7.bam \
--service "g" \
--reference hg19
```

Click [Use AGS to run a WGS workflow](https://aliware-images.oss-cn-hangzhou.aliyuncs.com/ACK/%E5%9B%BE%E7%89%87/wgs.svg) to watch a demonstration of how to use AGS CLI to run a WGS workflow.

## Start a Mapping workflow

Use --fastq1 and --fastq2 to specify FASTQ files and use --output to specify the output path of bam.

```
Usage:

ags remote run mapping \
--region cn-shenzhen # region of oss, e.g. cn-shenzhen, cn-beijing and etc\
--fastq1 MGISEQ/MGISEQ2000_PCR-free_NA12878_1_V100003043_L01_1.fq.gz # filename of fastq pair 2, fastq-path\filename \
--fastq2 MGISEQ/MGISEQ2000_PCR-free_NA12878_1_V100003043_L01_2.fq.gz  # filename of fastq pair 1\
--bucket my-test-shenzhen # Bucket name\
--output-bam bam/MGISEQ_NA12878_hs37d5.bam # Output filename of BAM \
--service "g" #SLA: [n:normal|s:silver|g:gold|p:platinum]\
--markdup [true|false|default true] #Mark Duplicated, by default true
--reference [hg19|hg38|<reference path on OSS>]

e.g.

ags remote run mapping \
--region cn-shenzhen \
--fastq1 MGISEQ/MGISEQ2000_PCR-free_NA12878_1_V100003043_L01_1.fq.gz  \
--fastq2 MGISEQ/MGISEQ2000_PCR-free_NA12878_1_V100003043_L01_2.fq.gz  \
--bucket my-test-shenzhen \
--output-bam bam/MGISEQ_NA12878_hs37d5.bam # Output filename of BAM \
--service "g" \
--markdup "true" \
--reference hg19
            
```

Click [Use AGS to run a Mapping workflow](https://aliware-images.oss-cn-hangzhou.aliyuncs.com/ACK/%E5%9B%BE%E7%89%87/mapping.svg) to watch a demonstration of how to use AGS CLI to run a Mapping workflow.

## List remote workflows

```
Usage:
ags remote list

e.g.
ags remtoe list
+---------------+-------------------------------+
|   JOB NAME    |          CREATE TIME          |
+---------------+-------------------------------+
| wgs-gpu-ckw96 | 2020-01-07 19:08:32 +0000 UTC |
| wgs-gpu-djzws | 2020-01-07 18:31:22 +0000 UTC |
| wgs-gpu-pd659 | 2020-01-03 20:34:09 +0000 UTC |
+---------------+-------------------------------+
```

## Obtain workflow details

```
Usage:
ags remote get <workflow id> --show
--show show detail of input parameters of workflow

e.g.
ags remote get wgs-gpu-sjtlw
+---------------+------------------+-----------+-------------------------------+----------+-------------------------------+
|   JOB NAME    |  JOB NAMESPACE   |  STATUS   |          CREATE TIME          | DURATION |          FINISH TIME          |
+---------------+------------------+-----------+-------------------------------+----------+-------------------------------+
| wgs-gpu-sjtlw | XXXXXXXXXXXXXXXX | Succeeded | 2020-01-07 21:38:05 +0800 CST | 12m25s   | 2020-01-07 21:50:30 +0800 CST |
+---------------+------------------+-----------+-------------------------------+----------+-------------------------------+

ags remote get wgs-gpu-97xfn --show

+---------------+------------------+-----------+-------------------------------+----------+-------------------------------+
|   JOB NAME    |  JOB NAMESPACE   |  STATUS   |          CREATE TIME          | DURATION |          FINISH TIME          |
+---------------+------------------+-----------+-------------------------------+----------+-------------------------------+
| wgs-gpu-sjtlw | XXXXXXXXXXXXXXXX | Succeeded | 2020-01-07 21:38:05 +0800 CST | 12m25s   | 2020-01-07 21:50:30 +0800 CST |
+---------------+------------------+-----------+-------------------------------+----------+-------------------------------+


+-----------------------+---------------------------------+
|      JOB DETAIL       |                                 |
+-----------------------+---------------------------------+
| wgs_reference_file    | hg19                          |
| wgs_service           | g                               |
| wgs_oss_region        | cn-shenzhen                     |
| wgs_fastq_first_name  | MGISAMPLE001                    |
| wgs_fastq_second_name | MGISAMPLE001                    |
| wgs_bucket_name       | my-test-shenzhen                |
| wgs_vcf_file_name     | vcf/MGISEQ_NA12878_hs37d5_6.vcf |
| wgs_bam_file_name     | bam/MGISEQ_NA12878_hs37d5_6.bam |
+-----------------------+---------------------------------+

            
```

## Cancel a running workflow

```
Usage:

ags remote cancel  <workflow id>

e.g.

ags remote cancel wgs-gpu-zls6r
INFO[0000] Successed to cancel wgs-gpu-zls6r
```

## Remove a finished workflow

You can remove successful and failed workflows. However, you cannot remove running workflows.

```
Usage: 

ags remote remove <workflow id>

e.g.

ags remote remove wgs-gpu-zls6r
INFO[0000] Successed to remove wgs-gpu-zls6r
```

