---
keyword: [AGS服务, 加速工作流]
---

# 通过AGS服务调用加速工作流

通过本地的WDL工作流和远程的AGS工作流组成的混合工作流，可以将耗时长、计算量大、标准流程的任务放在AGS服务端中执行。将耗时短、所需资源有限、需要自行定制流程的任务放在本地的WDL工作流中执行，数据通过OSS进行中间流转，从而有效的提高任务执行效率、节省资源成本。本文将通过RemoteApi实现Mapping过程为例，演示如何编写并运行一个AGS混合工作流。

-   [开通服务](https://common-buy.aliyun.com/?commodityCode=csk_gspayasgo_public_cn#/buy)
-   已在应用目录中安装ack-ags-wdl。更多信息，请参见[创建WDL工作流](/cn.zh-CN/基因计算服务AGS用户指南/WDL工作流/创建WDL工作流.md)的[步骤一：部署应用](/cn.zh-CN/基因计算服务AGS用户指南/WDL工作流/创建WDL工作流.mdsection_50p_xvd_2by)。

## 操作步骤

1.  配置AGS。

    1.  下载和安装AGS。具体操作，请参见[AGS命令行帮助](/cn.zh-CN/基因计算服务AGS用户指南/AGS工作流/AGS命令行帮助.md)。

        ```
        ags config init
        ```

        配置AGS完成后，会自动生成AGS配置文件config。

    2.  将AGS配置文件config保存在/ags-wdl-nas/bwatest/ags/config路径下。

2.  创建agsHybrid.wd文件。

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

3.  创建agsHybrid.json文件。

    以下文件中的/ags-wdl-oss/、/ags-wdl-nas/需要设置为部署ack-ags-wdl的Bucket和NAS的路径。

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
         "wf.bwa_mem_tool.fastqFolder": "/ags-wdl-nas/sample/MGISEQ2000_PCR-free_NA12878_1_V100003043_L01_1.fq.gz", #任务执行的工作目录
         "wf.uploaddata.naspath": "/ags-wdl-nas/sample",
         "wf.uploaddata.osspath": "/ags-wdl-oss/output/bam",
         }   
    ```

4.  创建混合工作流。

    ```
    ags wdl run agsHybrid.wdl agsHybrid.json
    ```

    创建混合工作流成功后，会自动执行混合工作流。

    找到ags-wdl-oss对应Bucket，进入Bucket下的output/bam，可以看到gene.bam.bai文件。说明混合工作流创建并执行成功。


