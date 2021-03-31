---
keyword: [AGS服务, 加速工作流, AGS混合工作流]
---

# 通过AGS服务调用加速工作流

通过本地工作流和AGS远程工作流组成的混合工作流，可以将耗时长、计算量大、标准流程的任务放在AGS服务端执行。将耗时短、所需资源有限、需要自行定制流程的任务放在本地工作流中执行，数据通过OSS进行中间流转，从而有效的提高任务执行效率、节省资源成本。本文将通过RemoteAPI实现Mapping过程为例，演示如何编写并运行一个AGS混合工作流。

[开通服务](https://common-buy.aliyun.com/?commodityCode=csk_gspayasgo_public_cn#/buy)。

## 操作步骤

1.  配置AGS。

    关于AGS的下载和安装，请参见[AGS命令行帮助](/cn.zh-CN/基因计算服务AGS用户指南/AGS工作流/AGS命令行帮助.md)。

    ```
    ags config init
    ```

    配置AGS完成后，会自动生成AGS配置文件config。

2.  创建ConfigMap。

    ```
    kubectl create configmap config --from-file=~/.ags/config -n argo
    ```

3.  创建hybridStorage.yaml文件。

    根据实际情况修改以下YAML文件中的NAS的IP、目录，OSS的AKID、AKSecret、Bucket等参数。

    ```
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: gene-shenzhen-cache-nas
      namespace: argo
      labels:
        alicloud-pvname: gene-shenzhen-cache-nas
    spec:
      capacity:
        storage: 20Gi
      storageClassName: alicloud-nas
      accessModes:
        - ReadWriteMany
      csi:
        driver: nasplugin.csi.alibabacloud.com
        volumeHandle: gene-shenzhen-cache-nas-pvc
        volumeAttributes:
          server: "xxxxxxxx-fbi71.cn-beijing.nas.aliyuncs.com"
          path: "/tarTest"
      mountOptions:
      - nolock,tcp,noresvport
      - vers=3 
    ---
    kind: PersistentVolumeClaim
    apiVersion: v1
    metadata:
      name: gene-shenzhen-cache-nas-pvc
      namespace: argo
    spec:
      accessModes:
        - ReadWriteMany
      resources:
        requests:
          storage: 20Gi
      storageClassName: alicloud-nas
      selector:
        matchLabels:
          alicloud-pvname: gene-shenzhen-cache-nas
    ---  
    apiVersion: v1
    kind: Secret
    metadata:
      name: oss-secret
      namespace: argo
    stringData:
      akId: xxxxxxxxxx
      akSecret: xxxxxxxxxxx
    ---
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: gene-shenzhen-cache-oss-pvc
      namespace: argo
    spec:
      accessModes:
      - ReadWriteMany
      resources:
        requests:
          storage: 200Gi
      selector:
        matchLabels:
          alicloud-pvname: gene-shenzhen-cache-oss   
    ---
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: gene-shenzhen-cache-oss
      namespace: argo
      labels:
        alicloud-pvname: gene-shenzhen-cache-oss  
    spec:
      capacity:
        storage: 200Gi
      accessModes:
        - ReadWriteMany
      persistentVolumeReclaimPolicy: Retain
      csi:
        driver: ossplugin.csi.alibabacloud.com
        volumeHandle: gene-shenzhen-cache-oss // 需要和PV名字一致。
        nodePublishSecretRef:
          name: oss-secret
          namespace: argo
        volumeAttributes:
          bucket: "oss-test-tsk"
          url: "oss-cn-beijing.aliyuncs.com"
          otherOpts: "-o max_stat_cache_size=0 -o allow_other"
    ```

4.  创建存储数据的PV和PVC。

    ```
    kubectl create -f hybridStorage.yaml
    ```

5.  创建hybrid.yaml文件。

    ```
    apiVersion: argoproj.io/v1alpha1
    kind: Workflow                  #new type of k8s spec
    metadata:
      generateName: mpileup-    #name of workflow spec
      namespace: argo
    spec:
      entrypoint: mpileup         #invoke the whalesay template
      arguments:
        parameters:
    
        # fastq define
        - name: bucket
          value: "my-test-shenzhen"
        - name: fastqDir
          value: "sample"
        - name: fastq1
          value: "MGISEQ2000_PCR-free_NA12878_1_V100003043_L01_1.fq.gz"
        - name: fastq2
          value: "MGISEQ2000_PCR-free_NA12878_1_V100003043_L01_2.fq.gz"
        - name: reference
          value: "hg19"
    
        # gene define
        - name: bamDir
          value: "outbam/bam"
        - name: vcfDir
          value: "output/vcf"
        - name: bamFileName
          value: "gene.bam"
        - name: cpuNumber
          value: "32"
        - name: vcfFileName
          value: "gene.vcf"
        - name: service
          value: "s"
    
      volumes:
      - name: workdir
        persistentVolumeClaim:
          claimName: gene-shenzhen-cache-nas-pvc
      - name: outputdir
        persistentVolumeClaim:
          claimName: gene-shenzhen-cache-oss-pvc
      - name: agsconfig
        configMap:
          name: config
    
      templates:
      #It is executed remotely and accelerated
      - name: agsmapping 
        container:
          image: registry.cn-beijing.aliyuncs.com/shuangkun/genetool:v1.1
          imagePullPolicy: Always
          command: [sh,-c]
          args:
          - ags remote run mapping --region cn-shenzhen --fastq1 sample/{{workflow.parameters.fastq1}} --fastq2 sample/{{workflow.parameters.fastq2}} --bucket {{workflow.parameters.bucket}} --output-bam {{workflow.parameters.bamDir}}/{{workflow.parameters.bamFileName}} --reference {{workflow.parameters.reference}} --service s --watch;
          volumeMounts:
          - name: agsconfig
            mountPath: /root/.ags/config
            subPath: config
            
      #Download BAM file from OSS to NAS
      - name: mpileupprepare
        container:
          image: registry.cn-beijing.aliyuncs.com/shuangkun/genetool:v1.1
          imagePullPolicy: Always
          command: [sh,-c]
          args:
          - cp /output/outbam/bam/gene.bam /data/sample/. 
          resources:
            requests:
              memory: 8Gi
              cpu: 4
          volumeMounts:
          - name: workdir
            mountPath: /data
          - name: outputdir
            mountPath: /output  
      #It is executed locally
      - name: samtoolsindex
        retryStrategy:
          limit: 3
        container:
          image: registry.cn-beijing.aliyuncs.com/shuangkun/genetool:v1.1
          command: [sh,-c]
          args:
          - samtools index -@ {{workflow.parameters.cpuNumber}} /data/sample/gene.bam
          volumeMounts:
          - name: workdir
            mountPath: /data
          resources:
            requests:
              memory: 20Gi
              cpu: 4
      
      ##Upload index file from NAS to OSS
      - name: upload
        retryStrategy:
          limit: 3
        container:
          image: registry.cn-beijing.aliyuncs.com/shuangkun/genetool:v1.1
          command: [sh,-c]
          args:
          - cp /data/sample/gene.bam.bai /output/outbam/bam/.
          volumeMounts:
          - name: workdir
            mountPath: /data
          - name: outputdir
            mountPath: /output    
          resources:
            requests:
              memory: 20Gi
              cpu: 4      
              
              
      - name: mpileup
        dag:
          tasks:
          - name: agsmappingtask
            template: agsmapping
    
          - name: download-data
            dependencies: [agsmappingtask]
            template: mpileupprepare
    
          - name: index
            dependencies: [download-data]
            template: samtoolsindex
            
          - name: upload-data
            dependencies: [index]
            template: upload 
    ```

6.  创建混合工作流。

    ```
    kubectl create -f hybrid.yaml
    ```

    创建混合工作流成功后，会自动执行混合工作流。

    找到gene-shenzhen-cache-oss-pvc对应的Bucket，进入Bucket下的/outbam/bam/，可以看到gene.bam.bai，说明混合工作流创建并执行成功。


