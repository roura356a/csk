# 创建WDL工作流

WDL（Workflow Description Language）是由Broad Institute开发的一种流程开发语言，简单易用，能够有效的提高生物信息工作流的构建效率。本文介绍如何通过AGS在ACK集群上编写并执行WDL工作流。

-   已创建ACK集群，详细介绍请参见[创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes托管版集群.md)。
-   已拥有一个或多个存储服务（NAS存储卷或者符合NFS协议的文件存储、OSS存储），用于存储输入数据和输出结果，详细介绍请参见[使用NAS动态存储卷](/cn.zh-CN/Kubernetes集群用户指南/存储管理-CSI/NAS存储卷/使用NAS动态存储卷.md)。

## 在ACK上运行WDL的优势

-   兼容社区CromwellServer，完整兼容WDL的流程定义，对遗留流程无需修改，便可以通过AGS在ACK上运行WDL流程。WDL的详细介绍请参见[WDL](https://openwdl.org/)。
-   对Task资源申请优化，通过Pod Guarantee QoS方式，避免资源过度争取造成节点负载过高和效率下降。
-   与阿里云存储的无缝整合，目前支持直接访问OSS和NAS，并支持多数据源的挂载。

## 与AGS工作流的区别

-   CronwellServer在以下方面仍然落后于云原生的AGS工作流。
    -   资源控制粒度（CPU、Mem min、Mem max）方面。
    -   调度优化，自动重试，资源上限的动态调整。
    -   监控、日志等方面。
-   在集群资源使用水位上低于AGS，在批量样本投递成功率上也低于AGS工作流。对于大批量重复性的工作流仍然建议改造成原生的AGS工作流来提升效率，详细介绍请参见[创建工作流](/cn.zh-CN/基因计算服务AGS用户指南/AGS工作流/创建工作流.md)。
-   对于高CPU消耗的Mapping、HC、Mutecv2等流程可以使用AGS API来降低处理成本节省和加速，详细介绍请参见[通过AGS处理全基因组测序WGS](/cn.zh-CN/基因计算服务AGS用户指南/AGS无服务器化API加速/通过AGS处理全基因组测序WGS.md)。

## 步骤一：部署应用

构建WDL工作流所需的组件被打包成为一个Helm Chart，作为一个应用放在应用市场中，避免了复杂的环境配置，方便进行一键部署。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，选择**市场** \> **应用目录**。

3.  在应用目录页面右侧搜索框中搜索ack-ags-wdl，找到ack-ags-wdl，然后单击ack-ags-wdl。

4.  在应用目录 - ack-ags-wdl页面单击**参数**页签。

5.  配置应用参数。

    ```
    # PVCs to be mounted
    naspvcs:
      - naspvc1
      - naspvc2
    osspvcs:
      - osspvc1
      - osspvc2
      
    # config in transfer-pvc.yaml、storageclass.yaml
    naspvc1:
      # modify it to actual url of NAS/NFS server .
      server : "XXXXXX-fbi71.cn-beijing.nas.aliyuncs.com"
      # absolute path of your data on NAS/NFS, pls modify it to your actual path of data root
      path: "/tarTest" #eg "/tarTest"
      # strorage driver. flexVolume or csi,default is csi, if your kubernetes use flexVolume,chang it to flexVolume
      driver: "csi" # by default is csi, you could change to flexVolume for early version of K8s (<= 1.14.x)
      nasbasepath: "/ags-wdl-nas"
      # mountoptions. if you use local NFS, modify it to your mount options.
      # config in storageclass.yaml 、transfer-pvc.yaml
      mountVers: "3" #mount version-svc:
      mountOptions: "nolock,tcp,noresvport"  # mount options, for local NFS server, you could remove noresvport option
    
    naspvc2:
      # modify it to actual url of NAS/NFS server .
      server : "XXXXXXX-fbi71.cn-beijing.nas.aliyuncs.com"
      # absolute path of your data on NAS/NFS, pls modify it to your actual path of data root
      path: "/tarTest/bwatest" #eg "/tarTest"
      # strorage driver. flexVolume or csi,default is csi, if your kubernetes use flexVolume,chang it to flexVolume
      driver: "csi" # by default is csi, you could change to flexVolume for early version of K8s (<= 1.14.x)
      nasbasepath: "/ags-wdl-nas2"
      # mountoptions. if you use local NFS, modify it to your mount options.
      # config in storageclass.yaml 、transfer-pvc.yaml
      mountVers: "3" #mount version-svc:
      mountOptions: "nolock,tcp,noresvport"  # mount options, for local NFS server, you could remove noresvport option
    
    osspvc1:
      # modify it to actual bucket name
      bucket: "oss-test-tsk"
      # modify it to actual bucket url
      url: "oss-cn-beijing.aliyuncs.com"
      # mount options. It is not changed by default.
      options: "-o max_stat_cache_size=0 -o allow_other"
      akid: "XXXXXXX"
      aksecret: "XXXXXXX"
      # absolute path of your data on OSS
      path: "/"
      ossbasepath: "/ags-wdl-oss"
    
    osspvc2:
      bucket: "oss-test-tsk"
      url: "oss-cn-beijing.aliyuncs.com"
      options: "-o max_stat_cache_size=0 -o allow_other"
      akid: "XXXXXXX"
      aksecret: "XXXXXXX"
      path: "/input"
      ossbasepath: "/ags-wdl-oss-input"
    
    # Relative root path of input data and output data in your wdl.json, your path should add basepath before the relative path of the mount.
    # e.g. 
    # {
    # "wf.bwa_mem_tool.reference": "/ags-wdl/reference/subset_assembly.fa.gz",#reference filename。
    # "wf.bwa_mem_tool.reads_fq1": "/ags-wdl/fastq_sample/SRR1976948_1.fastq.gz",#fastq1 filename
    # "wf.bwa_mem_tool.reads_fq2": "/ags-wdl/fastq_sample/SRR1976948_2.fastq.gz",#fastq2 filename
    # "wf.bwa_mem_tool.outputdir": "/ags-wdl/bwatest/output", #output path,the result will output in xxxxxxxx.cn-beijing.nas.aliyuncs.com:/mydata_root/bwatest/output,you should make sure the path exists.
    # "wf.bwa_mem_tool.fastqFolder": "fastqfolder" #任务执行的工作目录
    # }
    
    # workdir, you can choose nasbasepath or ossbasepath as your workdir
    workdir : "/ags-wdl-oss"
    # provisiondir,you can choose nasbasepath or ossbasepath as your provisiondir. task will create a volume dynamic to input/output date.
    provisiondir: "/ags-wdl-oss-input"
    #Scheduling the task to the specified node. e.g. nodeselector: "node-type=wdl"
    nodeselector: "node-type=wdl"
    #config in cromwellserver-svc.yaml
    cromwellserversvc:
      nodeport : "32567" # cromwellserver nodeport, for internal access, you could use LB instead for external access to cromwell server.
    
    #config in config.yaml
    config:
      # project namespace. default is wdl，Generally, no change is required
      namespace: "wdl" # namespace
      #tesk backand domain name. default is tesk-api，Generally, no change is required
      teskserver: "tesk-api"
      #cromwellserver domain name,default is cromwellserver，Generally, no change is required
      cromwellserver: "cromwellserver"
      #cromwellserver port,default is 8000，Generally, no change is required
      cromwellport: "8000"
    ```

    -   配置应用存储卷。

        应用存储卷包括NAS和OSS。

        |参数|说明|是否必须配置|配置方法|
        |--|--|------|----|
        |naspvcs|需要挂载的NAS存储卷|是|当需要挂载的NAS存储卷PVC为`naspvc1`、`naspvc2`时，配置如下。        ```
naspvcs:
  - naspvc1
  - naspvc2
        ``` |
        |osspvcs|需要挂载的OSS存储卷|是|当需要挂载的OSS存储卷PVC为`osspvc1`、`osspvc2`时，配置如下。        ```
osspvcs:
  - osspvc1
  - osspvc2
        ``` |

    -   配置NAS存储。

        每一个NAS存储卷对应执行任务容器中的一个路径`nasbasepath`。配置`naspvcs`需要为每一个NAS存储卷进行详细的参数配置。

        **说明：** 其他参数配置保持默认即可。

        |参数|说明|是否必须配置|配置方法|
        |--|--|------|----|
        |server|NAS IP|是|输入和输出数据。需要修改为您自己NAS的url，和path。|
        |path|挂载的子目录|是|
        |driver|Flexvolume or CSI|是|集群的存储驱动，默认为CSI。当集群使用Flexvolume时，需将该参数修改为Flexvolume。|
        |nasbasepath|NAS目录在应用中的映射目录|是|NAS目录在应用中的映射目录，在后续输入数据和输出数据时，需要将绝对目录中的`server:path`替换为`basepath`。例如，`server`为192.168.0.1，`path`为/wdl，`basepath`为/ags-wdl，输入数据test.fq.gz在NAS中的存储目录为192.168.0.1:/wdl/test/test.fq.gz。当需要填写输入路径时，应填写为/ags-wdl/test/test.fq.gz，此时应用能够访问到输入数据。本文后面均以`server`为192.168.0.1，`path`为/wdl，`basepath`为/ags-wdl作为示例。|
        |mountOptions|挂载选项|是|挂载参数。当使用您自己的NFS时间时，可以修改该参数进行适配。|
        |mountVers|挂载版本|是|

    -   配置OSS存储。

        每一个OSS存储卷对应执行任务容器中的一个路径`ossbasepath`。配置`osspvcs`需要为每一个OSS存储卷进行详细的参数配置。

        |参数|说明|是否必须配置|配置方法|
        |--|--|------|----|
        |bucket|oss bucket|是|需要修改为您自己OSS的bucket name和url。|
        |url|oss url|是|
        |options|挂载参数|是|默认为`-o max_stat_cache_size=0 -o allow_other`，不用修改。|
        |akid|akid|是|您的ak信息，将会以Secret的方式部署在集群中。|
        |aksecret|aksecret|是|
        |path|挂载的子目录|是|需要挂载的bucket的子目录。|
        |ossbasepath|OSS目录在应用中的映射目录|是|OSS目录在应用中的映射目录，在后续输入数据和输出数据时，需要将绝对目录中的`bucket:path`替换为`ossbasepath`。例如，`bucket`为shenzhen-test，`path`为/wdl，`ossbasepath`为/ags-wdl，输入数据test.fq.gz在oss中的存储目录为shenzhen-test:/wdl/test/test.fq.gz。当需要填写输入路径时，应填写为/ags-wdl/test/test.fq.gz，此时应用能够正确的访问到输入数据。|

    -   配置其他参数。

        |参数|说明|是否必须配置|配置方法|
        |--|--|------|----|
        |workdir|工作目录|是|需要在配置的`nasbasepath`、`ossbasepath`中选择一个作为工作目录，任务运行过程中的中间文件（script、error等）均会生成在该目录下。|
        |provisiondir|动态创建pv目录|是|需要在配置的`nasbasepath`、`ossbasepath`中选择一个作为动态创建PV的目录，任务运行过程中的输入输出数据均会生成在该目录下创建的PV中。|
        |nodeselector|任务调度支持label|否|如果需要将任务调度到特定的label机器时，可以填写该字段。配置`node-type=wdl`会调度所有的任务到`node-type=wdl`的节点上。|
        |nodeport|cromwellserver的服务暴露端口|是|cromwellserver的服务暴露端口。当您需要用自己的Widdler客户端提交任务时，需要用到该参数，默认为32567。|
        |namespace|项目命名空间|是|应用部署的命名空间，默认为wdl。|
        |teskserver|tesk服务域名|是|tesk的服务域名，默认无需更改。|
        |cromwellserver|cromwellserver|是|cromwellserver的服务域名，默认无需更改。|
        |cromwellport|cromwellserver端口|是|cromwellserver的服务端口，默认无需更改。|

6.  部署应用。

    在应用目录 - ack-ags-wdl页面右侧选择集群和命名空间，设置发布名称，单击**创建**。

    **说明：**

    执行以下命令，返回的结果显示cromwellcli、cromwellserver、tesk-api三个组件正常运行，说明部署成功。

    ```
    kubectl get pods -n wdl
    ```

    系统输出类似如下结果。

    ```
    NAME                                READY   STATUS      RESTARTS   AGE
    cromwellcli-85cb66b98c-bv4kt        1/1     Running     0          5d5h
    cromwellserver-858cc5cc8-np2mc      1/1     Running     0          5d5h
    tesk-api-5d8676d597-wtmhc           1/1     Running     0          5d5h
    ```


## 步骤二：提交任务

在集群外部可以通过AGS和命令行提交任务，推荐使用AGS提交任务。

**通过AGS提交任务**

新版本AGS-CLI提供了向集群提交WDL任务的功能，您只需下载AGS，配置crowellserver地址即可。下载AGS请参见[AGS命令行帮助](/cn.zh-CN/基因计算服务AGS用户指南/AGS工作流/AGS命令行帮助.md)。

1.  创建bwa.wdl文件和bwa.json文件。

    -   bwa.wdl文件示例

        ```
        task bwa_mem_tool {
          Int threads
          Int min_seed_length
          Int min_std_max_min
          String reference
          String reads_fq1
          String reads_fq2
          String outputdir
          String fastqFolder
          command {
                mkdir -p /bwa/${fastqFolder}
                cd /bwa/${fastqFolder}
                rm -rf SRR1976948*
                wget https://ags-public.oss-cn-beijing.aliyuncs.com/alignment/subset_assembly.fa.gz
                wget https://ags-public.oss-cn-beijing.aliyuncs.com/alignment/SRR1976948_1.fastq.gz
                wget  https://ags-public.oss-cn-beijing.aliyuncs.com/alignment/SRR1976948_2.fastq.gz
                gzip -c ${reference} > subset_assembly.fa
                gunzip -c ${reads_fq1} | head -800000 > SRR1976948.1
                gunzip -c ${reads_fq2} | head -800000 > SRR1976948.2
                bwa index subset_assembly.fa
                bwa aln subset_assembly.fa SRR1976948.1 > ${outputdir}/SRR1976948.1.untrimmed.sai
                bwa aln subset_assembly.fa SRR1976948.2 > ${outputdir}/SRR1976948.2.untrimmed.sai
          }
          output {
            File sam1 = "${outputdir}/SRR1976948.1.untrimmed.sai"
            File sam2 = "${outputdir}/SRR1976948.2.untrimmed.sai"
          }
          runtime {
            docker: "registry.cn-hangzhou.aliyuncs.com/plugins/wes-tools:v3"
            memory: "2GB"
            cpu: 1
          }
        }
        workflow wf {
          call bwa_mem_tool
        }
        ```

    -   bwa.json文件示例

        ```
        {
        "wf.bwa_mem_tool.reference": "subset_assembly.fa.gz",#reference filename。
        "wf.bwa_mem_tool.reads_fq1": "SRR1976948_1.fastq.gz",#fastq1 filename
        "wf.bwa_mem_tool.reads_fq2": "SRR1976948_2.fastq.gz",#fastq2 filename
        "wf.bwa_mem_tool.outputdir": "/ags-wdl/bwatest/output", #output path,the result will output in 192.168.0.1:/wdl/bwatest/output,you should make sure the path exists.
        "wf.bwa_mem_tool.fastqFolder": "fastqfolder" #任务执行的工作目录
        }
        ```

2.  将访问集群中的32567端口的流量转发到service cromwellserver上。

    ```
    kubectl port-forward svc/cromwellserver 32567:32567
    ```

3.  配置cromwellserver地址。

    输入以下命令。

    ```
    ags config init
    ```

    系统输出类似如下结果。

    ```
    Please input your AccessKeyID
    xxxxx
    Please input your AccessKeySecret
    xxxxx
    Please input your cromwellserver url
    192.168.0.1:32567
    ```

4.  在本地提交WDL任务。

    输入以下命令。

    ```
    ags wdl run resource/bwa.wdl resource/bwa.json --watch #watch参数可以让该命令进行一个同步等待，直到该任务成功或失败。
    ```

    系统输出类似如下结果。

    ```
    INFO[0000] bd747360-f82c-4cd2-94e0-b549d775f1c7 Submitted
    ```

5.  您还可以在本地进行查询、删除WDL任务。

    -   查询WDL任务。
        -   通过explain命令查询WDL任务。

            输入以下命令。

            ```
            ags wdl explain bd747360-f82c-4cd2-94e0-b549d775f1c7
            ```

            系统输出如下结果。

            ```
            INFO[0000] bd747360-f82c-4cd2-94e0-b549d775f1c7 Running
            ```

        -   通过query命令查询WDL任务。

            输入以下命令。

            ```
            ags wdl query bd747360-f82c-4cd2-94e0-b549d775f1c7
            ```

            系统输出如下结果。

            ```
            INFO[0000] bd747360-f82c-4cd2-94e0-b549d775f1c7 {"calls":{"end":"0001-01-01T00:00:00.000Z","executionStatus":null,"inputs":null,"start":"0001-01-01T00:00:00.000Z"},"end":"0001-01-01T00:00:00.000Z","id":"b3aa1563-6278-4b2e-b525-a2ccddcbb785","inputs":{"wf_WGS.Reads":"/ags-wdl-nas/c.tar.gz"},"outputs":{},"start":"2020-10-10T09:34:56.022Z","status":"Running","submission":"2020-10-10T09:34:49.989Z"}
            ```

    -   删除WDL任务。

        输入以下命令。

        ```
        ags wdl abort bd747360-f82c-4cd2-94e0-b549d775f1c7
        ```

        系统输出如下结果。

        ```
        INFO[0000] bd747360-f82c-4cd2-94e0-b549d775f1c7 Aborting
        ```


**通过命令行提交任务**

您需要编写WDL文件和输入的JSON文件，通过提供的镜像提交任务，其中cromwellserver的地址为集群IP：nodeport。

1.  创建wa.wdl文件和bwa.json文件。

    -   bwa.wdl文件示例

        ```
        task bwa_mem_tool {
          Int threads
          Int min_seed_length
          Int min_std_max_min
          String reference
          String reads_fq1
          String reads_fq2
          String outputdir
          String fastqFolder
          command {
                mkdir -p /bwa/${fastqFolder}
                cd /bwa/${fastqFolder}
                rm -rf SRR1976948*
                wget https://ags-public.oss-cn-beijing.aliyuncs.com/alignment/subset_assembly.fa.gz
                wget https://ags-public.oss-cn-beijing.aliyuncs.com/alignment/SRR1976948_1.fastq.gz
                wget  https://ags-public.oss-cn-beijing.aliyuncs.com/alignment/SRR1976948_2.fastq.gz
                gzip -c ${reference} > subset_assembly.fa
                gunzip -c ${reads_fq1} | head -800000 > SRR1976948.1
                gunzip -c ${reads_fq2} | head -800000 > SRR1976948.2
                bwa index subset_assembly.fa
                bwa aln subset_assembly.fa SRR1976948.1 > ${outputdir}/SRR1976948.1.untrimmed.sai
                bwa aln subset_assembly.fa SRR1976948.2 > ${outputdir}/SRR1976948.2.untrimmed.sai
          }
          output {
            File sam1 = "${outputdir}/SRR1976948.1.untrimmed.sai"
            File sam2 = "${outputdir}/SRR1976948.2.untrimmed.sai"
          }
          runtime {
            docker: "registry.cn-hangzhou.aliyuncs.com/plugins/wes-tools:v3"
            memory: "2GB"
            cpu: 1
          }
        }
        workflow wf {
          call bwa_mem_tool
        }
        ```

    -   bwa.json文件示例

        ```
        {
        "wf.bwa_mem_tool.reference": "subset_assembly.fa.gz",#reference filename。
        "wf.bwa_mem_tool.reads_fq1": "SRR1976948_1.fastq.gz",#fastq1 filename
        "wf.bwa_mem_tool.reads_fq2": "SRR1976948_2.fastq.gz",#fastq2 filename
        "wf.bwa_mem_tool.outputdir": "/ags-wdl/bwatest/output", #output path,the result will output in 192.168.0.1:/wdl/bwatest/output,you should make sure the path exists.
        "wf.bwa_mem_tool.fastqFolder": "fastqfolder" #任务执行的工作目录
        }
        ```

2.  提交WDL任务。

    输入以下命令。

    ```
    docker run -e CROMWELL_SERVER=192.16*.*.** -e CROMWELL_PORT=30384 registry.cn-beijing.aliyuncs.com/tes-wes/cromwellcli:v1 run resources/bwa.wdl resources/bwa.json
    ```

    系统输出类似如下结果。

    ```
    -------------Cromwell Links-------------
    http://192.16*.*.**:30384/api/workflows/v1/5d7ffc57-6883-4658-adab-3f508826322a/metadata
    http://192.16*.*.**:30384/api/workflows/v1/5d7ffc57-6883-4658-adab-3f508826322a/timing
    {
        "status": "Submitted", 
        "id": "5d7ffc57-6883-4658-adab-3f508826322a"
    }
    ```


