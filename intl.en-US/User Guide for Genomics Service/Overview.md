---
keyword: [genomics computing, genomics service, AGS]
---

# Overview

Alibaba Cloud Genomics Service \(AGS\) is an ultra-fast, cost-effective, and high-precision genome sequencing and secondary analytics service that is developed by Alibaba Cloud. AGS provides containerized workflows for genomics computing based on Container Service for Kubernetes \(ACK\). AGS provides an out-of-the-box acceleration API. You can call this API without the need to create a cluster. This topic describes AGS and its benefits.

## What is AGS

AGS is commonly used in genome sequencing and secondary analytics. It requires only 15 minutes to complete a high-precision 30x whole genome sequencing \(WGS\) process, which includes gene comparison, sequencing, deduplication, and mutation detection. Compared with the traditional solution, AGS offers a 120 times speed improvement and is 2 to 4 times faster than the globally used FPGA/GPU solution.

AGS provides strong support for genetic disease detection and cancer screening by analyzing and identifying the mutation mechanism of personal genome sequences. In the future, it will play a bigger role in clinical medicine and genetic diagnosis. The whole human genome consists of about three billion base pairs. One 30x WGS sample is about 100 GB in size. AGS offers great benefits in terms of computation speed, precision, cost, usability, and compatibility with upstream sequencers. It is also suitable in scenarios such as SNP/INDEL and CNV detection in DNAs, and RNA virus detection.

## Benefits

-   Ultra-fast and high-precision

    In actual tests, the solution completes the sequencing and secondary analytics of eight 30x WGS samples within 15 minutes. The solution can be used to assemble, sequencing, deduplicate, and detect mutation on 720 billion base pairs within 15 minutes. The solution achieves a 120 times speed improvement without compromising the precision. If you compare the NA12878 sample and gold-standard Variant Call Format \(VCF\) files, the SNP precision reaches 99.80% and the precision of secondary analytics is higher or equivalent to the output of BWA-0.7.17/GATK 4.1.3.

    ![AGS_overview_01](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5386858951/p88760.png)

    |Dataset: 30x NA12878|
    |--------------------|
    |**SNP**|**RECALL**|**PRECISION**|**F1**|
    |GATK 4.1|99.86%|99.79%|99.82%|
    |AGS|99.86%|99.80%|99.83%|
    |**INDEL**|**RECALL**|**PRECISION**|**F1**|
    |GATK 4.1|99.28%|99.70%|99.49%|
    |AGS|99.27%|99.68%|99.47%|

-   Cost-effective

    ACK and AGS provide PaaS-based acceleration capabilities and assists BGI Group to perform secondary analytics on large-scale FASTQ data from genome sequencers based on a hybrid cloud architecture. This solution reduces the costs of secondary analytics and shortens the delivery cycle by 95%.

-   Wide service scope

    Meets flexible needs and supports custom analytics processes for different platforms and data types without compromising the throughput. Provides more simplified and efficient storage, automated analytics, data transmission, project collaboration, and bioinformation tool development solutions for major sequencing service providers and research institutions.

    AGS supports native Kubernetes workflows and allows you to execute DAG-structured workflows in Kubernetes clusters. The service is also applicable in scenarios such as genetic computing and data processing.

-   Easy to use

    AGS supports elastic scheduling in large-scale computing scenarios based on the auto scaling feature of the cloud network architecture. In the practice of this solution, you do not need to plan computing resources, processing logic, and data caching. You only need to upload data in FASTQ files to Object Storage Service \(OSS\) and authorize AGS to access your OSS buckets. The data analytics process will be efficiently completed and the result will be uploaded to your specified storage.


## Related topics

In addition, AGS also fixes the issues in workflow management, massive data storage, migration and transmission, and security compliance requirements. For more information, see the following topics:

-   [Use AGS to perform WGS tasks](/intl.en-US/User Guide for Genomics Service/AGS acceleration API/Use AGS to perform WGS tasks.md)
-   [Perform virus sequencing through AGS](/intl.en-US/User Guide for Genomics Service/AGS acceleration API/Perform virus sequencing through AGS.md)
-   [Use AGS to analyze tumor samples](/intl.en-US/User Guide for Genomics Service/AGS acceleration API/Use AGS to analyze tumor samples.md)
-   [Use workflows in Kubernetes clusters](https://developer.aliyun.com/article/717193)
-   [Introduction to AGS CLI](/intl.en-US/User Guide for Genomics Service/AGS workflow/Introduction to AGS CLI.md)

