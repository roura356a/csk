---
keyword: [AGS, 分析肿瘤, mutect2]
---

# 通过AGS分析肿瘤样本

通过AGS调用`mutect2`任务来检测体细胞短突变，短突变包括单核苷酸（SNV）以及插入和缺失（InDel）的突变。本文介绍如何通过AGS分析肿瘤样本。

AGS `mutect2`支持两种模式的典型场景：

-   肿瘤加正常样本模式：肿瘤样本在分析过程跳过正常人的胚系变异。
-   肿瘤模式：对单个肿瘤样本的比对数据进行分析。

`mutect2`的体系变异检测是保持了和GATK4.1.3一致的变异检测方式，但提供了30~80倍的加速。针对90Gbase的比对数据，10分钟内可以完成变异检测。

## mutect2使用示例

单击[检测AGS Mutect2肿瘤](https://aliware-images.oss-cn-hangzhou.aliyuncs.com/ACK/image/mutect2.svg)，将为您演示如何通过命令检测AGS Mutect2肿瘤。

## 在肿瘤加正常样本模式下分析样本

以给定匹配的正常样本作为基准，`mutect2`仅检测体细胞变异。`mutect2`会根据提供的证据（例如在匹配的正常人中），实现跳过在胚系中明显存在的变异的逻辑，以避免在胚系事件上花费计算资源。

-   用法

    执行命令：

    ```
    ags remote run mutect2 \
    --region cn-shenzhen # region of oss, e.g. cn-shenzhen, cn-beijing and etc\
    --bucket my-test-shenzhen # Bucket name\
    --input-bam-tumor bam/HKU2_160660.bam #Tumor sample bam file\
    --input-bam-normal  bam/MGISEQ_NA12878_RG_HG38.bam  # Optional normal sample bam \
    --bed bed/performance.blocks.exp.bed # Optional target bed \
    --output-vcf vcf/HKU2_160660.vcf  # Output filename\
    --service "s" #SLA: [n:normal|s:silver|g:gold|p:platinum]\
    --reference [hg19|hg38|<reference path on OSS>] # hg19: it is hs37d5 version, GRCh37/hg19 include decoy contig, no support for UCSC hg19. hg38: GRCh38/hg38 include decoy
    ```

-   预期输出：

```
ags remote run mutect2 \
--region cn-shenzhen \
--bucket my-test-shenzhen \
--input-bam-tumor bam/HKU2_160660.bam \
--input-bam-normal  bam/MGISEQ_NA12878_RG.bam \
--output-vcf vcf/HKU2_160660.vcf \
--service "s"  \
--reference hg19
INFO[0001] {"JobName":"mutect2-gpu-vp7d9"}
INFO[0001] Job submit succeed

ags remote get mutect2-gpu-vp7d9 --show
+-------------------+------------------+---------+-------------------------------+---------------+-------------+-------------+
|     JOB NAME      |  JOB NAMESPACE   | STATUS  |          CREATE TIME          |   DURATION    | TOTAL READS | TOTAL BASES |
+-------------------+------------------+---------+-------------------------------+---------------+-------------+-------------+
| mutect2-gpu-vp7d9 | XXXXXXXXX | Running | 2020-04-10 16:02:39 +0800 CST | 36.311883677s |           0 |           0 |
+-------------------+------------------+---------+-------------------------------+---------------+-------------+-------------+


+--------------------------+---------------------------+
|        JOB DETAIL        |                           |
+--------------------------+---------------------------+
| mutect2_reference_group  |                           |
| mutect2_oss_region       | cn-shenzhen               |
| mutect2_bucket_name      | my-test-shenzhen          |
| mutect2_output_vcf_name  | vcf/HKU2_160660.vcf       |
| mutect2_reference_file   | hg19                      |
| mutect2_input_bam_tumor  | bam/HKU2_160660.bam       |
| mutect2_input_bam_normal | bam/MGISEQ_NA12878_RG.bam |
| mutect2_input_bed        |                           |
| mutect2_service          | s                         |
+--------------------------+---------------------------+
```


## 在单独肿瘤样本模式下分析样本

此模式对单一类型的样本（例如肿瘤或正常样本）进行分析。

-   用法

    执行命令：

    ```
    ags remote run mutect2 \
    --region cn-shenzhen # region of oss, e.g. cn-shenzhen, cn-beijing and etc\
    --bucket my-test-shenzhen # Bucket name\
    --input-bam-tumor bam/HKU2_160660.bam #Tumor／Normal sample bam file\
    --output-vcf vcf/HKU2_160660.vcf  # Output filename\
    --service "s" #SLA: [n:normal|s:silver|g:gold|p:platinum]\
    --reference [hg19|hg38|<reference path on OSS>] # hg19: it is hs37d5 version, GRCh37/hg19 include decoy contig, no support for UCSC hg19. hg38: GRCh38/hg38 include decoy
    ```

-   预期输出：

    ```
    ags remote run mutect2 \
    --region cn-shenzhen \
    --bucket my-test-shenzhen \
    --input-bam-tumor bam/HKU2_160660.bam \
    --output-vcf vcf/HKU2_160660.all.vcf \
    --service "s"  \
    --reference hg19
    INFO[0001] {"JobName":"mutect2-gpu-6tc8s"}
    INFO[0001] Job submit succeed
    
    ags remote get mutect2-gpu-6tc8s --show
    +-------------------+------------------+-----------+-------------------------------+----------+-------------------------------+-------------+-------------+
    |     JOB NAME      |  JOB NAMESPACE   |  STATUS   |          CREATE TIME          | DURATION |          FINISH TIME          | TOTAL READS | TOTAL BASES |
    +-------------------+------------------+-----------+-------------------------------+----------+-------------------------------+-------------+-------------+
    | mutect2-gpu-6tc8s | XXXXXXXXXX | Succeeded | 2020-04-10 15:51:59 +0800 CST | 4m12s    | 2020-04-10 15:56:11 +0800 CST |           0 |           0 |
    +-------------------+------------------+-----------+-------------------------------+----------+-------------------------------+-------------+-------------+
    
    
    +--------------------------+-------------------------+
    |        JOB DETAIL        |                         |
    +--------------------------+-------------------------+
    | mutect2_oss_region       | cn-shenzhen             |
    | mutect2_input_bam_tumor  | bam/HKU2_160660.bam     |
    | mutect2_input_bam_normal |                         |
    | mutect2_input_bed        |                         |
    | mutect2_output_vcf_name  | vcf/HKU2_160660.all.vcf |
    | mutect2_bucket_name      | my-test-shenzhen        |
    | mutect2_reference_file   | hg19                    |
    | mutect2_reference_group  |                         |
    | mutect2_service          | s                       |
    +--------------------------+-------------------------+
    ```


