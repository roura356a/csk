# Analyze tumor samples through AGS

Alibaba Cloud Genomics Service \(AGS\) enables you to call `mutect2` tasks to detect somatic mutations, including single-nucleotide variants \(SNVs\) and insertions and deletions \(Indels\). This topic describes how to use AGS to analyze tumor samples.

AGS `mutect2` supports the following two scenarios:

-   Tumor and normal samples: Skip germline mutations in normal individuals during the analysis process.
-   Tumor samples: Perform correlation analysis on individual tumor samples.

`mutect2` adopts the same mutation detection methods as GATK4.1.3 but provides 30 to 80 times speed improvements. AGS can complete mutation detection on 90 billion base pairs within 10 minutes.

## Analyze tumor and normal samples

Based on normal samples with a given match, mutect2 detects somatic mutations only. mutect2 will skip the logic of mutations that are clearly normal in the germline based on the provided evidence, for example, matched normal individuals. This avoids wasting computing resources on germline events.

-   Usage:

    ```
    ags remote run mutect2 \
    ```

    ```
    --region cn-shenzhen # region of oss, e.g. cn-shenzhen, cn-beijing and etc\
    --bucket my-test-shenzhen # Bucket name\
    --input-bam-tumor bam/HKU2_160660.bam #Tumor sample bam file\
    --input-bam-normal  bam/MGISEQ_NA12878_RG_HG38.bam  # Optional normal sample bam \
    --bed bed/performance.blocks.exp.bed # Optional target bed \
    --output-vcf vcf/HKU2_160660.vcf  # Output filename\
    --service "s" #SLA: [n:normal|s:silver|g:gold|p:platinum]\
    --reference [hg19|hg38|<reference path on OSS>] # hg19: it is hs37d5 version, GRCh37/hg19 include decoy contig, no support for UCSC hg19. hg38: GRCh38/hg38 include decoy
    ```

-   Example:

    ```
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


## Analyze individual tumor samples

mutect2 performs analysis on individual samples, for example, tumor samples or normal samples.

-   Usage:

    ```
    ags remote run mutect2 \
    ```

    ```
    --region cn-shenzhen # region of oss, e.g. cn-shenzhen, cn-beijing and etc\
    --bucket my-test-shenzhen # Bucket name\
    --input-bam-tumor bam/HKU2_160660.bam #Tumor／Normal sample bam file\
    --output-vcf vcf/HKU2_160660.vcf  # Output filename\
    --service "s" #SLA: [n:normal|s:silver|g:gold|p:platinum]\
    --reference [hg19|hg38|<reference path on OSS>] # hg19: it is hs37d5 version, GRCh37/hg19 include decoy contig, no support for UCSC hg19. hg38: GRCh38/hg38 include decoy
    ```

-   Example:

    ```
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


