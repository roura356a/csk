# Create a WDL workflow

Workflow Description Language \(WDL\) is a language developed by Broad Institute. WDL specifies data processing workflows with a human-readable and writeable syntax. You can use WDL to efficiently create bioinformatics workflows. This topic describes how to use Alibaba Cloud Genomics Service \(AGS\) to create and run a WDL workflow in a cluster of Container Service for Kubernetes \(ACK\).

-   An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   One or more storage services such as Apsara File Storage NAS \(NAS\), Object Storage Service \(OSS\), or file storage systems that support the Network File System \(NFS\) protocol are deployed. These storage services are used to store input and output data. For more information, see [Mount a NAS file system as a dynamic volume](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/NAS volumes/Mount a NAS file system as a dynamic volume.md).

## Benefits of WDL

-   WDL is compatible with open source Cromwell, which supports WDL scripts. You can directly run WDL workflows in ACK clusters without the need to modify the legacy workflows. For more information about WDL, see [WDL](https://openwdl.org/).
-   WDL allows you to assign the Guaranteed quality of service \(QoS\) class to pods to optimize resource allocation for tasks. This prevents load increases and performance degradation caused by resource contention on nodes.
-   WDL is seamlessly integrated with Alibaba Cloud storage services. WDL can directly access OSS and NAS, and allows you to ingest data from multiple data sources.

## Differences between WDL workflows and AGS workflows

-   Compared with workflows run by a Cromwell server, cloud-native AGS workflows have the following benefits:
    -   Resource control: parameters such as CPU, Mem min, and Mem max.
    -   Scheduling optimization, automatic retry, and dynamic adjustments of resource quotas.
    -   Monitoring and logging.
-   WDL has a lower resource utilization compared with AGS workflows. Therefore, the success rate of sample delivery is also lower than AGS workflows. To create a large number of recurring workflows, we recommend that you use AGS workflows. For more information, see [Create a workflow](/intl.en-US/User Guide for Genomics Service/AGS workflow/Create a workflow.md).
-   To reduce relevant costs and accelerate CPU-consuming processes such as mapping, HaplotypeCaller \(HC\), and Mutect2, we recommend that you call the AGS API. For more information, see [Use AGS to perform WGS tasks](/intl.en-US/User Guide for Genomics Service/AGS acceleration API/Use AGS to perform WGS tasks.md).

## Step 1: Deploy an application

Select all components that are required for creating a WDL workflow and package them into a Helm chart. The Helm chart can be used as an application and be uploaded to the marketplace. This simplifies the configuration process.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **App Catalog**.

3.  On the App Catalog page, enter the keyword ack-ags-wdl in the search box to search for the ack-ags-wdl application, and then click the ack-ags-wdl card.

4.  On the App Catalog - ack-ags-wdl page, click the **Parameters** tab.

5.  Set the parameters.

    ```
    # PVCs to be mounted
    naspvcs:
      - naspvc1
      - naspvc2
    osspvcs:
      - osspvc1
      - osspvc2
      
    # Complete the configurations in the transfer-pvc.yaml and storageclass.yaml files.
    naspvc1:
      # modify it to actual url of NAS/NFS server .
      server : "XXXXXX-fbi71.cn-beijing.nas.aliyuncs.com"
      # absolute path of your data on NAS/NFS, pls modify it to your actual path of data root
      path: "/tarTest" #eg "/tarTest"
      # strorage driver. flexVolume or csi,default is csi, if your kubernetes use flexVolume,chang it to flexVolume
      driver: "csi" # by default is csi, you could change to flexVolume for early version of K8s (<= 1.14.x)
      nasbasepath: "/ags-wdl-nas"
      # mountoptions. if you use local NFS, modify it to your mount options.
      # Complete the configurations in the storageclass.yaml and transfer-pvc.yaml files.
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
      # Complete the configurations in the storageclass.yaml and transfer-pvc.yaml files.
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
    # "wf.bwa_mem_tool.reference": "/ags-wdl/reference/subset_assembly.fa.gz",# The name of the reference file.
    # "wf.bwa_mem_tool.reads_fq1": "/ags-wdl/fastq_sample/SRR1976948_1.fastq.gz",#fastq1 filename
    # "wf.bwa_mem_tool.reads_fq2": "/ags-wdl/fastq_sample/SRR1976948_2.fastq.gz",#fastq2 filename
    # "wf.bwa_mem_tool.outputdir": "/ags-wdl/bwatest/output", #output path,the result will output in xxxxxxxx.cn-beijing.nas.aliyuncs.com:/mydata_root/bwatest/output,you should make sure the path exists.
    # "wf.bwa_mem_tool.fastqFolder": "fastqfolder" # The directory where tasks are run.
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
      # The project namespace. The default value is wdl. You do not need to modify the value in most scenarios.
      namespace: "wdl" # namespace
      #The TESK backand domain name. The default value is tesk-api. You do not need to modify the value in most scenarios.
      teskserver: "tesk-api"
      #The domain name of the Cromwell server. The default value is cromwellserver. You do not need to modify the value in most scenarios.
      cromwellserver: "cromwellserver"
      #The port of the Cromwell server. The default value is 8000. You do not need to modify the value in most scenarios.
      cromwellport: "8000"
    ```

    -   Mount volumes to store application data.

        You can use NAS and OSS to store application data.

        |Parameter|Description|Required|Configuration method|
        |---------|-----------|--------|--------------------|
        |naspvcs|The NAS file system that you want to mount to the ACK cluster as a volume.|Yes|If the persistent volume claims \(PVCs\) of the NAS file system are `naspvc1` and `naspvc2`, the configuration is:         ```
naspvcs:
  - naspvc1
  - naspvc2
        ``` |
        |osspvcs|The OSS bucket that you want to mount to the ACK cluster as a volume.|Yes|If the PVCs of the OSS bucket are `osspvc1` and `osspvc2`, the configuration is:         ```
osspvcs:
  - osspvc1
  - osspvc2
        ``` |

    -   Configure the NAS file system.

        Each NAS file system mounted to an ACK cluster corresponds to a `nasbasepath` path in the container. To set the `naspvcs` parameter, you must configure each NAS file system.

        **Note:** Leave the default setting for other parameters.

        |Parameter|Description|Required|Configuration method|
        |---------|-----------|--------|--------------------|
        |server|NAS IP|Yes|The server is used to read and write data. You must specify the URL and path of the NAS file system that you want to mount to the ACK cluster.|
        |path|The subdirectory of the NAS file system.|Yes|
        |driver|Flexvolume or CSI|Yes|Enter the storage driver of the cluster. The default driver is Container Storage Interface \(CSI\). If the cluster uses FlexVolume, change the value to Flexvolume.|
        |nasbasepath|The application directory to which the NAS directory is mapped.|Yes|Enter the application directory to which the NAS directory is mapped. To write data to and read data from the NAS file system, you must replace `server:path` in the absolute path with `basepath`. For example, `server` is set to 192.168.0.1, `path` is set to /wdl, and `basepath` is set to /ags-wdl. In this case, the file test.fq.gz is stored in the directory 192.168.0.1:/wdl/test/test.fq.gz in the NAS file system. If you want to enter an absolute path, enter /ags-wdl/test/test.fq.gz. Then, the application can access data written to this path. In this topic, `server` is set to 192.168.0.1, `path` is set to /wdl, and `basepath` is set to /ags-wdl.|
        |mountOptions|The mount options.|Yes|Set the parameters required for mounting the NAS file system. If you use a self-managed NAS file system, you must modify this parameter.|
        |mountVers|Enter the version of the NAS file system.|Yes|

    -   Configure the OSS bucket.

        Each OSS bucket mounted to an ACK cluster corresponds to an `ossbasepath` path in the cluster. To set the `osspvcs` parameter, you must configure each OSS bucket.

        |Parameter|Description|Required|Configuration method|
        |---------|-----------|--------|--------------------|
        |bucket|oss bucket|Yes|You must specify the URL and path of the OSS bucket that you want to mount to the ACK cluster.|
        |url|oss url|Yes|
        |options|The mount options.|Yes|The default is `-o max_stat_cache_size=0 -o allow_other`. You do not need to modify the value.|
        |akid|akid|Yes|Enter your AccessKey pair, which is saved in a Secret in the ACK cluster.|
        |aksecret|aksecret|Yes|
        |path|The subdirectory of the OSS bucket.|Yes|Enter the subdirectory of the OSS bucket that you want to mount to the ACK cluster.|
        |ossbasepath|The application directory to which the OSS directory is mapped.|Yes|Enter the application directory to which the OSS directory is mapped. To write data to and read data from the OSS bucket, you must replace `bucket:path` with `ossbasepath` in the absolute path. For example, `bucket` is set to shenzhen-test, `path` is set to /wdl, and `ossbasepath` is set to /ags-wdl. The test.fq.gz file is stored in the directory shenzhen-test:/wdl/test/test.fq.gz in the OSS bucket. When you enter the input path, enter /ags-wdl/test/test.fq.gz. Then, the application can access data written to this path.|

    -   Set other parameters.

        |Parameter|Description|Required|Configuration method|
        |---------|-----------|--------|--------------------|
        |workdir|The directory where files that are generated by tasks are stored.|Yes|Specify `nasbasepath` or `ossbasepath` as the directory. All files including scripts and error log files that are generated by tasks are stored in the specified directory.|
        |provisiondir|The directory of dynamically provisioned persistent volumes \(PVs\).|Yes|Specify `nasbasepath` or `ossbasepath` as the directory. All input and output data generated by tasks are stored in the PVs in the specified directory.|
        |nodeselector|Allows you to schedule tasks by label.|No|You can set this parameter to schedule tasks to nodes with specified labels. If you set `node-type=wdl`, all tasks are scheduled to nodes with the label `node-type=wdl`.|
        |nodeport|The port that is open on the Cromwell server.|Yes|Enter the port that is open on the Cromwell server. If you want to use the Widdler command-line interface \(CLI\) tool to submit tasks, you must set this parameter. The default value is 32567.|
        |namespace|The namespace of the project.|Yes|Enter the namespace where the application is deployed. The default value is wdl.|
        |teskserver|The domain name of the TESK Service.|Yes|Enter the domain name of the TESK Service. You can use the default value.|
        |cromwellserver|cromwellserver|Yes|Enter the domain name of the Cromwell server. You can use the default value.|
        |cromwellport|The port of the Cromwell server.|Yes|Enter the port that is open on the Cromwell server. You can use the default value.|

6.  Deploy the application.

    On the App Catalog - ack-ags-wdl page, find the Deploy section on the right side of the page. Select the cluster and namespace, enter a name for the application, and then click **Create**.

    **Note:**

    Run the following command. If the output shows that the cromwellcli, cromwellserver, and tesk-api components run as expected, it indicates that the application has been deployed.

    ```
    kubectl get pods -n wdl
    ```

    Sample output:

    ```
    NAME                                READY   STATUS      RESTARTS   AGE
    cromwellcli-85cb66b98c-bv4kt        1/1     Running     0          5d5h
    cromwellserver-858cc5cc8-np2mc      1/1     Running     0          5d5h
    tesk-api-5d8676d597-wtmhc           1/1     Running     0          5d5h
    ```


## Step 2: Submit tasks

You can use AGS or the CLI to submit tasks from external systems. We recommend that you use AGS to submit tasks.

**Use AGS to submit tasks**

The latest version of the AGS CLI allows you to submit WDL tasks. To use AGS to submit tasks, download AGS and specify the address of the Cromwell server. For more information about how to download AGS, see [Introduction to AGS CLI](/intl.en-US/User Guide for Genomics Service/AGS workflow/Introduction to AGS CLI.md).

1.  Create a bwa.wdl file and a bwa.json file.

    -   Sample bwa.wdl file:

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

    -   Sample bwa.json file:

        ```
        {
        "wf.bwa_mem_tool.reference": "subset_assembly.fa.gz",# The name of the reference file.
        "wf.bwa_mem_tool.reads_fq1": "SRR1976948_1.fastq.gz",#fastq1 filename
        "wf.bwa_mem_tool.reads_fq2": "SRR1976948_2.fastq.gz",#fastq2 filename
        "wf.bwa_mem_tool.outputdir": "/ags-wdl/bwatest/output", #output path,the result will output in 192.168.0.1:/wdl/bwatest/output,you should make sure the path exists.
        "wf.bwa_mem_tool.fastqFolder": "fastqfolder" #The directory where files generated by tasks are stored.
        }
        ```

2.  Redirect requests that are destined for port 32567 to the Cromwell server.

    ```
    kubectl port-forward svc/cromwellserver 32567:32567
    ```

3.  Specify the address of the Cromwell server.

    Run the following command:

    ```
    ags config init
    ```

    Sample output:

    ```
    Please input your AccessKeyID
    xxxxx
    Please input your AccessKeySecret
    xxxxx
    Please input your cromwellserver url
    192.168.0.1:32567
    ```

4.  Submit WDL tasks from your on-premises machine.

    Run the following command:

    ```
    ags wdl run resource/bwa.wdl resource/bwa.json --watch # The watch parameter can be used to specify that the command is synchronous. The next task does not start until the current one succeeds or fails. 
    ```

    Sample output:

    ```
    INFO[0000] bd747360-f82c-4cd2-94e0-b549d775f1c7 Submitted
    ```

5.  You can query or delete WDL tasks from your on-premises machine.

    -   Query WDL tasks
        -   Run the explain command to query WDL tasks.

            Run the following command:

            ```
            ags wdl explain bd747360-f82c-4cd2-94e0-b549d775f1c7
            ```

            Sample output:

            ```
            INFO[0000] bd747360-f82c-4cd2-94e0-b549d775f1c7 Running
            ```

        -   Run the query command to query WDL tasks.

            Run the following command:

            ```
            ags wdl query bd747360-f82c-4cd2-94e0-b549d775f1c7
            ```

            Sample output:

            ```
            INFO[0000] bd747360-f82c-4cd2-94e0-b549d775f1c7 {"calls":{"end":"0001-01-01T00:00:00.000Z","executionStatus":null,"inputs":null,"start":"0001-01-01T00:00:00.000Z"},"end":"0001-01-01T00:00:00.000Z","id":"b3aa1563-6278-4b2e-b525-a2ccddcbb785","inputs":{"wf_WGS.Reads":"/ags-wdl-nas/c.tar.gz"},"outputs":{},"start":"2020-10-10T09:34:56.022Z","status":"Running","submission":"2020-10-10T09:34:49.989Z"}
            ```

    -   Delete WDL tasks

        Run the following command:

        ```
        ags wdl abort bd747360-f82c-4cd2-94e0-b549d775f1c7
        ```

        Sample output:

        ```
        INFO[0000] bd747360-f82c-4cd2-94e0-b549d775f1c7 Aborting
        ```


**Use the CLI to submit tasks**

To use the CLI to submit tasks, you must create a WDL file and a JSON file. Then, use the returned image to submit tasks. Specify the address of the Cromwell server in the following format: cluster IP address:node port.

1.  Create a bwa.wdl file and a bwa.json file.

    -   Sample bwa.wdl file:

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

    -   Sample bwa.json file:

        ```
        {
        "wf.bwa_mem_tool.reference": "subset_assembly.fa.gz",# The name of the reference file.
        "wf.bwa_mem_tool.reads_fq1": "SRR1976948_1.fastq.gz",#fastq1 filename
        "wf.bwa_mem_tool.reads_fq2": "SRR1976948_2.fastq.gz",#fastq2 filename
        "wf.bwa_mem_tool.outputdir": "/ags-wdl/bwatest/output", #output path,the result will output in 192.168.0.1:/wdl/bwatest/output,you should make sure the path exists.
        "wf.bwa_mem_tool.fastqFolder": "fastqfolder" # The directory where files generated by tasks are stored.
        }
        ```

2.  Submit the WDL tasks.

    Run the following command:

    ```
    docker run -e CROMWELL_SERVER=192.16*.*.** -e CROMWELL_PORT=30384 registry.cn-beijing.aliyuncs.com/tes-wes/cromwellcli:v1 run resources/bwa.wdl resources/bwa.json
    ```

    Sample output:

    ```
    -------------Cromwell Links-------------
    http://192.16*.*.**:30384/api/workflows/v1/5d7ffc57-6883-4658-adab-3f508826322a/metadata
    http://192.16*.*.**:30384/api/workflows/v1/5d7ffc57-6883-4658-adab-3f508826322a/timing
    {
        "status": "Submitted", 
        "id": "5d7ffc57-6883-4658-adab-3f508826322a"
    }
    ```


