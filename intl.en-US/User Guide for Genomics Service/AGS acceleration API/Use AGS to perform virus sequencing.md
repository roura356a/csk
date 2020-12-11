---
keyword: [AGS, genomics compute service, virus sequencing]
---

# Use AGS to perform virus sequencing

In the process of pathogen identification, it is an accurate and effective strategy to compare the samples under test with the genome databases of known pathogens. Alibaba Cloud Genomics Service \(AGS\) supports quick comparison of metagenome sequencing data. This topic describes how to use AGS to perform virus sequencing.

You have applied for the [public preview of AGS](https://survey.aliyun.com/apps/zhiliao/k6mkBtyn) and your application is approved.

**Note:** If your account is a Resource Access Management \(RAM\) user, you must also provide the UID of the RAM user for the application. You can log on to the [Account Management console](https://account.console.aliyun.com/?#/secure) to obtain the UID of the RAM user.

Coronavirus SARS-CoV-2 RNA \(hereinafter referred to as COVID-19\) can be detected in the upper or lower respiratory tract with a few weeks after the onset of the disease. At present, many types of RT-PCR kits are available to diagnose COVID-19. RT-PCR tests are cost-effective and can provide feedback within a short period of time. However, due to factors such as virus concentration and kit quality, RT-PCR tests are prone to false negatives. Multiple tests are required for confirmation, and only one specific virus can be checked at a time.

Metagenomic next-generation sequencing \(mNGS\) for pan-pathogen detection directly extracts a certain proportion of nucleic acid fragments \(including a large amount of human nucleic acids and a small amount of microbial nucleic acids\) from clinical samples for sequencing, data comparison with database engines, and bioinformation analysis. This enables unbiased identification of pathogenic microorganisms. This technology has made great contributions to the early detection and accurate sequencing of COVID-19.

Compared with RT-PCR tests, mNGS testing requires longer detection periods but provides higher accuracy. It can check multiple virus types at a time and monitor the mutations that may occur during the transmission of viruses. This allows you to enhance the prevention and control of viruses. Clinically, it is advisable to check suspected patients again, especially those who cannot be diagnosed by fluorescent quantitative PCR. This further improves the accuracy of test results and effectively prevents false negatives caused by virus mutations. After the genome of the pathogen is identified and updated in databases, the pathogen can be accurately detected by comparing nucleic acid sequences.

AGS supports quick comparison of mNGS data. It can complete the comparison of 3.2 billion base pairs \(22 million reads\) of an alveolar sample and the reference sequence of known pathogen genomes \(including COVID-19, 39 BetaCov RNAs, and 9,334 known viruses\) within 60 seconds. It also allows you to upload custom virus libraries to compare with samples under test. You only need to create an Object Storage Service \(OSS\) bucket and run AGS commands to complete the entire comparison process. AGS provides reports about high-quality alignments of reads and supports detection of multiple pathogen types. This provides solid data support for researches on the protein and mutations of COVID-19.

Genome sequencing service providers, disease control centers, hospitals, universities and research institutes, and pharmaceutical companies are all welcome to [apply for trial](https://survey.aliyun.com/apps/zhiliao/k6mkBtyn?spm=a2c4g.11186623.2.18.14dc1d74iy56o8).

## Preparations

1.  Set up AGS. For more information about how to download and install AGS CLI, see [Introduction to AGS CLI](/intl.en-US/User Guide for Genomics Service/AGS workflow/Introduction to AGS CLI.md).

    Sample command:

    ```
    ags config init
    ```

2.  For more information about how to download and install ossutil, see [Download and installation](/intl.en-US/Tools/ossutil/Download and installation.md).

3.  Create an Alibaba Cloud account, activate OSS, and create an OSS bucket to store mNGS data. For example, the URL of the OSS bucket may be oss://my-test-shenzhen. For more information, see [Get started with OSS](/intl.en-US/Quick Start/Get started with OSS.md).

    **Note:** If your account is a RAM user, we recommend that you create a new OSS bucket under the account to ensure that the OSS bucket is owned by the RAM user. You can run the following command to check the owner of the OSS bucket:

    ```
    ossutil stat oss://<your new bucket name>
    ```

4.  Grant AGS the permissions to call GetBucketInfo. This allows AGS to obtain information about your OSS bucket.

    **Note:**

    -   Make sure that the OSS bucket that you want to use is owned by the current account. Otherwise, we recommend that you create a new OSS bucket under the account and grant the permissions to call GetBucketInfo.
    -   If your account is a RAM user, you must attach the AliyunOSSFullAccess policy to the RAM user. For more information, see [Grant permissions to a RAM user](/intl.en-US/RAM User Management/Grant permissions to a RAM user.md).
    Sample command:

    ```
    ags config oss <bucket_name>
    ```


## Examples of rna-mapping

Click [Use AGS to test rna-mapping viruses](https://aliware-images.oss-cn-hangzhou.aliyuncs.com/ACK/image/rna.svg) to watch a video tutorial of how to use AGS CLI to test rna-mapping viruses.

## Compare with COVID-19

1.  Run ossutil commands to upload mNGS data to your OSS bucket.

    Sample command:

    ```
    ossutil cp  ICU6G_S2_L001_R1_001.fastq.gz oss://my-test-shenzhen/cov2-samples/
    ossutil cp  ICU6G_S2_L001_R2_001.fastq.gz oss://my-test-shenzhen/cov2-samples/
    ```

2.  Run a task to compare mNGS data with the known RNA sequence data.

    This topic compares sequencing sample ICU6G\_S2\_L001 with COVID-19.

    Sample command:

    ```
    Usage:
    ags remote run rna-mapping \ # <rna-mapping>: RNA sequence comparison task.
    --region <region_id> \ #
    <cn-shenzhen|cn-beijing|... >: region ID. Only the China (Shenzhen) and China (Beijing) regions are supported.
    --bucket <bucket_name> \ # <bucket_name> The name of the OSS bucket.
    --fastq1 <path_fq1> \ # Path of sequence data fq1.
    --fastq2 <path_fq2>\ # Path of sequence data fq2.
    --output-bam <path_of_output_bam> \ # Output path of comparison results in BAM format. The report is in the same path in TXT format.
    --reference [sars-cov-2 | betacov-ncbi-39 | viral-9334 | <path of RNA library reference in specified bucket >] # The reference sequence presents COVID-19 and 39 known BetaCov RNAs. You can also specify a custom virus sequence library.
    ```

    Sample command:

    ```
    ags remote run rna-mapping \
    --region cn-shenzhen \
    --fastq1 cov2-samples/ICU6G_S2_L001_R1_001.fastq.gz \
    --fastq2 cov2-samples/ICU6G_S2_L001_R2_001.fastq.gz  \
    --bucket my-test-shenzhen \
    --output-bam bam/ICU6G_S2.bam  \
    --reference sars-cov-2
    
    
    INFO[0002] {"JobName":"rna-mapping-gpu-2ms6w"}
    INFO[0002] Job submit succeed
    ```

3.  Check the comparison results.

    In the preceding example, the comparison of 10 million reads of mNGS data and COVID-19 sequence MN908947.3 produced 3,629 high-quality alignments of reads, and the number of reads whose alignment score \(AS\) exceeds 120 in the characteristic interval of COVID-19 was 404. This indicates that COVID-19 RNA sequences can be accurately detected in the sequence data.

    Sample results:

    ```
    ags remote get rna-mapping-gpu-2ms6w --show
    +-----------------------+------------------+-----------+-------------------------------+----------+-------------------------------+-------------+-------------+
    |       JOB NAME        |  JOB NAMESPACE   |  STATUS   |          CREATE TIME          | DURATION |          FINISH TIME          | TOTAL READS | TOTAL BASES |
    +-----------------------+------------------+-----------+-------------------------------+----------+-------------------------------+-------------+-------------+
    | rna-mapping-gpu-2ms6w | XXXXXXXXXXXX | Succeeded | 2020-03-04 16:40:30 +0800 CST | 43s      | 2020-03-04 16:41:13 +0800 CST |    10369818 |  1456539874 |
    +-----------------------+------------------+-----------+-------------------------------+----------+-------------------------------+-------------+-------------+
    
    
    +---------------------------------+--------------------------------------------+
    |           JOB DETAIL            |                                            |
    +---------------------------------+--------------------------------------------+
    | rna_matached_reads              |                                        480 |
    | rna_is_sars_cov2                | True                                       |
    | rna_mapping_oss_region          | cn-shenzhen                                |
    | rna_mapping_fastq_second_name   | cov2-samples/ICU6G_S2_L001_R2_001.fastq.gz |
    | rna_mapping_no_unmapped         |                                            |
    | rna_mapping_service             | s                                          |
    | rna_matached_reads_alignment    |                                        404 |
    | rna_high_quality_mapped         |                                       3629 |
    | rna_mapping_fastq_first_name    | cov2-samples/ICU6G_S2_L001_R1_001.fastq.gz |
    | rna_mapping_mark_dup            |                                            |
    | rna_mapping_reference_file_name | sars-cov-2                                 |
    | rna_cov_detail_file             | bam/ICU6G_S2.bam.cov.txt                   |
    | rna_mapping_bam_file_name       | bam/ICU6G_S2.bam                           |
    | rna_mapping_bucket_name         | my-test-shenzhen                           |
    +---------------------------------+--------------------------------------------+
    ```

4.  Run the following ossutil command to download the results and report.

    Sample command:

    ```
    ossutil ls oss://my-test-shenzhen/bam/ICU6G_S2.bam
    LastModifiedTime                   Size(B)  StorageClass   ETAG                                  ObjectName
    2020-03-04 16:41:11 +0800 CST       356320      Standard   9596D012A30438A0073A2A0B38F5D578      oss://my-test-shenzhen/bam/ICU6G_S2.bam
    2020-03-04 16:41:11 +0800 CST         2889      Standard   63175E7180D110BA9D3BAB34F4313C59      oss://my-test-shenzhen/bam/ICU6G_S2.bam.cov.txt
    2020-03-04 16:41:11 +0800 CST          396      Standard   940D51FF7ECFF60B5E5A41D1F635180D      oss://my-test-shenzhen/bam/ICU6G_S2.bam.summary.json
    
    ossutil cp oss://my-test-shenzhen/bam/HKU2_160660.summary.json .
    ossutil cp -r oss://my-test-shenzhen/bam/ICU6G_S2.bam.cov.txt .
    ossutil cp oss://my-test-shenzhen/bam/HKU2_160660.bam .
    ```

    Sample report:

    ```
    cat bam/ICU6G_S2.bam.cov.txt
    
    Summary:
    High Quality Mapped Reads is: 3629
    Matched reads in orf1ab range is: 480
    Matched reads in orf1ab range with alignment score (AS) is greater than 120: 404
    /data/cov2-samples_ICU6G_S2_L001_R1_001.fastq.gz-output/ICU6G_S2.bam is similar to SARS-CoV-2 with very high mappQ and AS reads: True
            21571     21581     21591     21601     21611     21621     21631
    NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN
    ATGTTTGTTTTTCTTGTTTTATTGCCACTAGTCTCTAGTCAGTGTGTTAATCTTACAACCAGAACTCAATTACCCCCTGC
    ATGTT GTTTTTCTTGTTTTATTGCCACTAGTCTCTAGTCAGTGTGTTAATCTTACAACCAGAACTCAATTACCCCCTGC
    ATGTTTGTTTTTCTTGTTTTATTGCCACTAGTCTCTAGT                           CAATTACCCCCTGC
    ATGTTTGTTTTTCTTGTTTTATTGCCACTAGTCTCTAGTCAGTGTGTTAATCTTACAACCAGA         CCCCCTGC
    ATGTTTGTTTTTCTTGTTTTATTGCCACTAGTCTCTAGTCAGTGTGTTAATCTTACAACCAGAACTCAATTACCCCCTGC
    ATGTTTGTTTTTCTTGTTTTATTGCCA  agtctctagtcagtgtgttaatcttacaaccagaactcaattaccccctgc
    atgtttgtttttcttgttttattgcca         AGTCAGTGTGTTAATCTTACAACCAGAACTCAATTACCCCCTGC
    ATGTTTGTTTTTCTTGTTTTATTGCCACTAGTCTCTAGTCAGTGTGTTAATCTTACAACCAGAACTCAATTACCCCCTGC
    atgtttgtttttcttgttttattgccactagtctctagtcagtgtgttaatcttacaaccagaactcaattaccccctgc
    atgtttgtttttcttgttttattgccactagtctctagtcagtgtgttaatcttacaaccagaactcaattaccccctgc
    atgtttgtttttcttgttttattgccactagtctctagtcagtgtgttaatcttacaaccagaactcaattaccccctgc
    atgtttgtttttcttgttttattgccactagtctctagtcagtgtgttaatcttacaaccagaactcaattaccccctgc
    ATGTTTGTTTTTCTTGTTTTATTGCCACTAGTCTCTAGTCAGTGTGTTAATCTTACAACCAGAACTCAATTACCCCCTGC
    ATGTTTGTTTTTCTTGTTTTATTGCCACTAGTCTCTAGTCAGTGTGTTAATCTTACAACCAGAACTCAATTACCCCCTGC
    atgtttgtttttcttgttttattgccactagtctctagtcagtgtgttaatcttacaaccagaactcaattaccccctgc
    atgtttgtttttcttgttttattgccactagtctctagtcagtgtgttaatcttacaaccagaactcaattaccccctgc
    atgtttgtttttcttgttttattgccactagtctctagtcagtgtgttaatcttacaaccagaactcaattaccccctgc
    atgtttgtttttcttgttttattgccactagtctctagtcagtgtgttaatcttacaaccagaactcaattaccccctgc
    atgtttgtttttcttgttttattgccactagtctctagtcagtgtgttaatcttacaaccagaactcaattaccccctgc
    atgtttgtttttcttgttttattgccactagtctctagtcagtgtgttaatcttacaaccagaactcaattaccccctgc
    atgtttgtttttcttgttttattgccactagtctctagtcagtgtgttaatcttacaaccagaactcaattaccccctgc
    ATGTTTGTTTTTCTTGTTTTATTGCCACTAGTCTCTAGTCAGTGTGTTAATCTTACAACCAGAACTCAATTACCCCCTGC
    atgtttgtttttcttgttttattgccactagtctctagtcagtgtgttaatcttacaaccagaactcaattaccccctgc
    ATGTTTGTTTTTCTTGTTTTATTGCCACTAGTCTCTAGTCAGTGTGTTAATCTTACAACCAGAACTCAATTACCCCCTGC
    ATGTTTGTTTTTCTTGTTTTATTGCCACTAGTCTCTAGTCAGTGTGTTAATCTTACAACCAGAACTCAATTACCCCCTGC
    atgtttgtttttcttgttttattgccactagtctctagtcagtgtgttaatcttacaaccagaactcaattaccccctgc
     TGTTTGTTTTTCTTGTTTT     CACTAGTCTCTAGTCAGTGTGTTAATCTTACAACCAGAACTCAATTACCCCCTGC
     tgtttgtttttcttgtttt
       TTTGTTTTTCTTGTTTTATTGCCACTAGTCTCTAGTCAGTGTGTTAATCTTACAACCAGAACTCAATTACCCCCTGC
          gtttttcttgttttattgccactagtctctagtcagtgtgttaatcttacaaccagaactcaattaccccctgc
    ```

5.  Further analyze the data.

    You can use tools such as samtools stats and plot-bamstats to further analyze the data and explore their similarities in coverage and depth. You can also perform protein composition analysis and mutation analysis.

    ![compare_02](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0486858951/p90974.png)

    ![compare_03](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0486858951/p90975.png)

6.  Repeat the preceding steps to compare COVID-19 with different samples.


## Compare with 39 known BetaCov RNAs

1.  Run ossutil commands to upload mNGS data to your OSS bucket.

    Sample command:

    ```
    ossutil cp  ICU6G_S2_L001_R1_001.fastq.gz oss://my-test-shenzhen/cov2-samples/
    ossutil cp  ICU6G_S2_L001_R2_001.fastq.gz oss://my-test-shenzhen/cov2-samples/
    ```

2.  Run a comparison task as shown in the following command.

    Sample command:

    ```
    ags remote run rna-mapping \
    --region cn-shenzhen \
    --fastq1 cov2-samples/ICU6G_S2_L001_R1_001.fastq.gz \
    --fastq2 cov2-samples/ICU6G_S2_L001_R2_001.fastq.gz  \
    --bucket my-test-shenzhen \
    --output-bam bam/ICU6G_S2_virus.bam  \
    --reference betacov-ncbi-39
    
    INFO[0011] {"JobName":"rna-mapping-gpu-6mpcc"}
    INFO[0011] Job submit succeed
    ```

3.  Check the comparison results.

    Sample results:

    ```
    ags remote get rna-mapping-gpu-6mpcc --show
    +-----------------------+------------------+-----------+-------------------------------+----------+-------------------------------+-------------+-------------+
    |       JOB NAME        |  JOB NAMESPACE   |  STATUS   |          CREATE TIME          | DURATION |          FINISH TIME          | TOTAL READS | TOTAL BASES |
    +-----------------------+------------------+-----------+-------------------------------+----------+-------------------------------+-------------+-------------+
    | rna-mapping-gpu-6mpcc | XXXXXXXXX | Succeeded | 2020-03-04 17:36:21 +0800 CST | 40s      | 2020-03-04 17:37:01 +0800 CST |    10369818 |  1456539874 |
    +-----------------------+------------------+-----------+-------------------------------+----------+-------------------------------+-------------+-------------+
    # 2014 mapped reads detected, but no mapped reads found in range
    +---------------------------------+--------------------------------------------+
    |           JOB DETAIL            |                                            |
    +---------------------------------+--------------------------------------------+
    | rna_mapping_reference_file_name | betacov-ncbi-39                            |
    | rna_matached_reads_alignment    |                                          0 |
    | rna_mapping_bam_file_name       | bam/ICU6G_S2_virus.bam                     |
    | rna_mapping_fastq_first_name    | cov2-samples/ICU6G_S2_L001_R1_001.fastq.gz |
    | rna_mapping_oss_region          | cn-shenzhen                                |
    | rna_cov_detail_file             | bam/ICU6G_S2_virus.bam.cov.txt             |
    | rna_mapping_no_unmapped         |                                            |
    | rna_matached_reads              |                                          0 |
    | rna_mapping_mark_dup            |                                            |
    | rna_mapping_service             | s                                          |
    | rna_high_quality_mapped         |                                       2014 |
    | rna_mapping_bucket_name         | my-test-shenzhen                           |
    | rna_mapping_fastq_second_name   | cov2-samples/ICU6G_S2_L001_R2_001.fastq.gz |
    | rna_is_sars_cov2                | False                                      |
    +---------------------------------+--------------------------------------------+
    ```


## Compare with a custom virus library

1.  Download reference sequences from [NCBI GeneBank](https://www.ncbi.nlm.nih.gov/nuccore) and merge them into multiple contigs.

    Example:

    Search reference sequences by keyword betacov and download the matches.

    ![compare_01](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0486858951/p90950.png)

2.  Rename the downloaded sequence file `sequence.fa` to `betacov-ncbi-test.fa`.

3.  Upload the reference sequence file to your OSS bucket.

    Sample command:

    ```
    ossutil cp betacov-ncbi-test.fa oss://my-test-shenzhen/ref/
    ```

4.  Run a comparison task and specify the path of the reference sequence, as shown in the following command.

    Sample command:

    ```
    ags remote run rna-mapping \
    --region cn-shenzhen \
    --fastq1 cov2-samples/ICU6G_S2_L001_R1_001.fastq.gz \
    --fastq2 cov2-samples/ICU6G_S2_L001_R2_001.fastq.gz  \
    --bucket my-test-shenzhen \
    --output-bam bam/ICU6G_S2_virus.bam  \
    --reference ref/betacov-ncbi-test.fa
    
    INFO[0002] {"JobName":"rna-mapping-gpu-69mwb"}
    INFO[0002] Job submit succeed
    ```

5.  View the report and download the results.

    Sample results:

    ```
    ags remote get rna-mapping-gpu-69mwb --show
    +-----------------------+------------------+-----------+-------------------------------+----------+-------------------------------+-------------+-------------+
    |       JOB NAME        |  JOB NAMESPACE   |  STATUS   |          CREATE TIME          | DURATION |          FINISH TIME          | TOTAL READS | TOTAL BASES |
    +-----------------------+------------------+-----------+-------------------------------+----------+-------------------------------+-------------+-------------+
    | rna-mapping-gpu-69mwb | 1365606736606053 | Succeeded | 2020-03-04 17:47:00 +0800 CST | 40s      | 2020-03-04 17:47:40 +0800 CST |    10369818 |  1456539874 |
    +-----------------------+------------------+-----------+-------------------------------+----------+-------------------------------+-------------+-------------+
    
    
    +---------------------------------+--------------------------------------------+
    |           JOB DETAIL            |                                            |
    +---------------------------------+--------------------------------------------+
    | rna_mapping_fastq_first_name    | cov2-samples/ICU6G_S2_L001_R1_001.fastq.gz |
    | rna_mapping_fastq_second_name   | cov2-samples/ICU6G_S2_L001_R2_001.fastq.gz |
    | rna_mapping_mark_dup            |                                            |
    | rna_mapping_oss_region          | cn-shenzhen                                |
    | rna_cov_detail_file             | bam/ICU6G_S2_virus.bam.cov.txt             |
    | rna_is_sars_cov2                | False                                      |
    | rna_mapping_bam_file_name       | bam/ICU6G_S2_virus.bam                     |
    | rna_mapping_service             | s                                          |
    | rna_matached_reads_alignment    |                                          0 |
    | rna_high_quality_mapped         |                                       2014 |
    | rna_mapping_bucket_name         | my-test-shenzhen                           |
    | rna_mapping_no_unmapped         |                                            |
    | rna_mapping_reference_file_name | ref/betacov-ncbi-test.fa                   |
    | rna_matached_reads              |                                          0 |
    +---------------------------------+--------------------------------------------+
    +---------------------------------+------------------------------------------+
    ```

6.  Download the result data for further analysis.

    Sample command:

    ```
    ossutil ls oss://my-test-shenzhen/bam/ICU6G_S2_virus.bam
    LastModifiedTime                   Size(B)  StorageClass   ETAG                                  ObjectName
    2020-03-04 17:47:38 +0800 CST       753458      Standard   DF7B1A6CA5AF5DE6BF4FFDBB6DEF71C3      oss://my-test-shenzhen/bam/ICU6G_S2_virus.bam
    2020-03-04 17:47:38 +0800 CST         1474      Standard   9D7968A779A0DE7C1993CC2A8D0E5A56      oss://my-test-shenzhen/bam/ICU6G_S2_virus.bam.cov.txt
    2020-03-04 17:47:38 +0800 CST          397      Standard   81170E30BAAFEB947A2238E015171A51      oss://my-test-shenzhen/bam/ICU6G_S2_virus.bam.summary.json
    Object Number is: 3
    
    ossutil cp oss://my-test-shenzhen/bam/ICU6G_S2_virus.bam.summary.json .
    
    cat bam/ICU6G_S2_virus.bam.summary.json
    {
        "total_reads":10369818,
        "total_bases":1456539874,
        "pass_vendor_filter_reads":10369818,
        "mapped_reads":6736,
        "pair_reads":6680,
        "properly_paired_reads":6520,
        "mapq_40_to_inf_reads":2030,
        "mapq_30_to_40_reads":0,
        "mapq_20_to_30_reads":1,
        "mapq_10_to_20_reads":3,
        "mapq_0_to_10_reads":23,
        "mapq_0_reads":10367761,
        "GC":"46.499%",
        "total_alignment":2057,
        "supplementary_alignment":0
    }%
    
    ossutil cp oss://my-test-shenzhen/bam/ICU6G_S2_virus.bam .
    samtools view bam/ICU6G_S2_virus.bam
                            
    ```


