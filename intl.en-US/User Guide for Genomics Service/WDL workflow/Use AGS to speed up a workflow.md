---
keyword: [speed up a workflow, AGS]
---

# Use AGS to speed up a workflow

You can compile a local workflow written in Workflow Description Language \(WDL\) and an Alibaba Cloud Genomics Service \(AGS\) workflow to form a hybrid workflow. We recommend that you run time-consuming tasks that have a standard workflow and require a large number of computing resources in the AGS workflow, and run other tasks in the local WDL workflow. Workflow data transfers through Object Storage Service \(OSS\). This improves operation efficiency and saves resources. This topic describes how to use AGS to create a mapping that allows you to compile and run a hybrid workflow.

-   The permissions to use AGS are granted. AGS is in public preview. Before you use this feature, make sure that you have the permissions. To apply for permissions, visit [AGS application](https://survey.aliyun.com/apps/zhiliao/k6mkBtyn).

    **Note:** If you log on as a RAM user, enter the ID of the RAM user when you apply for the use of AGS. You can obtain the ID of the RAM user in the [Account Management console](https://account.console.aliyun.com/?#/secure).

-   On the App Catalog page of the ACK console, ack-ags-wdl is installed.

## Procedure

1.  Configure AGS.

    1.  Download and install AGS. For more information, see [Introduction to AGS CLI](/intl.en-US/User Guide for Genomics Service/AGS workflow/Introduction to AGS CLI.md).

        ```
        ags config init
        ```

        After you configure AGS, the AGS configuration file config is generated.

    2.  Save config in the /ags-wdl-nas/bwatest/ags/config path.

2.  Add the following content to the agsHybrid.wd file.

    ```
    task agstask {
        String method
        String region
        String file1
        String file2
        String bucket
        String outputbam
        String ref
        String service
        String config
        command {
            mkdir /root/.ags
            cp ${config} /root/.ags/
            ags remote run ${method} --region ${region} --fastq1 ${file1} --fastq2 ${file2} --bucket ${bucket} --output-bam ${outputbam} --reference ${ref} --service ${service} --watch
        }
        runtime {
        docker: "registry.cn-beijing.aliyuncs.com/shuangkun/genetool:v1.1"
        memory: "20GB"
        cpu: 6
        }
      }
      task downloaddata {
        String osspath
        String naspath
        command {
            cp ${osspath}/gene.bam ${naspath}
        }
        runtime {
        docker: "ubuntu"
        memory: "2GB"
        cpu: 1
        }  
      }
      
      task bwa_mem_tool {
        String outputdir
        String fastqFolder
        String cpunum
        String bamfilename
        command {
            cd ${fastqFolder}
            samtools index -@ ${cpunum} ${bamfilename}
        }
        runtime {
        docker: "registry.cn-beijing.aliyuncs.com/shuangkun/genetool:v1.1"
        memory: "20GB"
        cpu: 6
        }
      }
      
      task uploaddata {
        String osspath
        String naspath
        command {
            cp ${naspath}/gene.bam.bai ${osspath}
        }
        runtime {
        docker: "ubuntu"
        memory: "2GB"
        cpu: 1
        }  
      }
      workflow wf {
          call agstask
          call downloaddata
          call bwa_mem_tool
          call uploaddata
      } 
    ```

3.  Add the following content to the agsHybrid.json file.

    You must mount ack-ags-wdl to the OSS bucket and the NAS volume. In the following code block, replace /ags-wdl-oss/ and /ags-wdl-nas/ with the paths where you mount ack-ags-wdl.

    ```
    {
         "wf.agstask.config": "/ags-wdl-nas/bwatest/ags/config",
         "wf.agstask.method": "mapping",
         "wf.agstask.region": "shenzhen",
         "wf.agstask.file1": "sample/MGISEQ2000_PCR-free_NA12878_1_V100003043_L01_1.fq.gz",
         "wf.agstask.file2": "sample/MGISEQ2000_PCR-free_NA12878_1_V100003043_L01_2.fq.gz",
         "wf.agstask.bucket": "my-test-shenzhen",
         "wf.agstask.outputbam": "output/bam/gene.bam",
         "wf.agstask.ref": "hg19",
         "wf.agstask.service": "s",
         "wf.downloaddata.osspath": "/ags-wdl-oss/output/bam",
         "wf.downloaddata.naspath": "/ags-wdl-nas/sample",
         "wf.bwa_mem_tool.cpunum": "6",#cpu num
         "wf.bwa_mem_tool.bamfilename": "gene.bam",
         "wf.bwa_mem_tool.outputdir": "/ags-wdl-nas/bwatest/output", #output path,the result will output in 192.168.0.1:/wdl/bwatest/output,you should make sure the path exists.
         "wf.bwa_mem_tool.fastqFolder": "/ags-wdl-nas/sample/MGISEQ2000 _PCR-free_NA12878_1_V100003043_L01_1.fq.gz", #The path where the tasks are running.
         "wf.uploaddata.naspath": "/ags-wdl-nas/sample",
         "wf.uploaddata.osspath": "/ags-wdl-oss/output/bam",
         }   
    ```

4.  Create a hybrid workflow.

    ```
    ags wdl run agsHybrid.wdl agsHybrid.json
    ```

    After you create a hybrid workflow, the workflow automatically runs.

    Find the bucket that corresponds to ags-wdl-oss, and go to the output/bam path of the bucket. If gene.bam.bai exists, the hybrid workflow is created and running properly.


