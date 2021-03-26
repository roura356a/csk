通过Sidecar采集日志 
==================================

日志服务支持在ECI中通过Sidecar模式采集日志。本文介绍如何安装Sidecar及使用控制台方式创建Logtail配置，完成容器日志的采集。

前提条件 
-------------------------

* Kubernetes集群中已正确部署virtual-kubelet。

  **说明**

  Serverless Kubernetes默认已经安装。关于如何创建ASK集群，请参见[创建Serverless Kubernetes集群](/intl.zh-CN/Serverless Kubernetes集群用户指南/集群/创建Serverless Kubernetes集群.md)。
  

* 集群已经开通日志服务。更多信息，请参见[日志服务](/intl.zh-CN/产品简介/什么是日志服务.md)。

  




背景信息 
-------------------------

**技术原理** 

日志可以分为两类：文件日志和标准输出。

文件日志是通过容器组内Volume共享实现的。容器组内同一个Volume可以挂载到多个容器，这样Sidecar可以直接收集业务容器写到相应的Volume内的文件日志。

标准输出日志依赖于ECI的[stdlog（FlexVolume）]()卷，创建容器组时可以将该卷挂载到Sidecar容器上，这样Sidecar可以直接以文件的方式访问ECI基础组件收集的业务标准输出日志。

**限制条件** 

Logtail必须和业务容器共享日志目录。

操作步骤 
-------------------------

**创建容器组** 

新建并部署以下YAML模板，创建一个以下样式的ECI（占位符变量需替换为实际值），用于演示采集日志到日志服务。

    apiVersion: apps/v1
    kind: Deployment
    metadata:
      labels:
        app: nginx-log-sidecar-demo
      name: nginx-log-sidecar-demo
    spec:
      replicas: 2
      selector:
        matchLabels:
          app: nginx-log-sidecar-demo
      template:
        metadata:
          labels:
            app: nginx-log-sidecar-demo
        spec:
          containers:
            - name: nginx-log-demo
              image: 'registry-vpc.${RegionId}.aliyuncs.com/log-service/docker-log-test:latest'
              command:
                - /bin/mock_log
              args:
                - '--log-type=nginx'
                - '--stdout=false'
                - '--stderr=true'
                - '--path=/var/log/nginx/access.log'
                - '--total-count=100000000'
                - '--logs-per-sec=100'
              imagePullPolicy: Always
              volumeMounts:
                - mountPath: /var/log/nginx
                  name: nginx-log
            - name: logtail
              image: 'registry-vpc.${RegionId}.aliyuncs.com/log-service/logtail:latest'
              env:
                - name: ALIYUN_LOGTAIL_USER_ID
                  value: '${Aliuid}'
                - name: ALIYUN_LOGTAIL_USER_DEFINED_ID
                  value: nginx-log-sidecar
                - name: ALIYUN_LOGTAIL_CONFIG
                  value: /etc/ilogtail/conf/${RegionId}/ilogtail_config.json
                - name: aliyun_logs_machinegroup
                  value: k8s-group-app-alpine
              imagePullPolicy: Always
              volumeMounts:
                - mountPath: /var/log/nginx
                  name: nginx-log
                - mountPath: /stdlog
                  name: stdlog
          volumes:
            - emptyDir: {}
              name: nginx-log
            - name: stdlog        # 标准输出卷
              flexVolume:
                driver: alicloud/pod-stdlog



**日志采集配置** 

1. 使用kubectl查询ContainerGroupId，该信息可以通过查询Pod的Annotations找到。

   ![1111](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4464031161/p230197.png)标准输出日志的目录结构及数据格式如下：![2222](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5464031161/p230339.png)访问[ECI控制台](https://eci.console.aliyun.com/)，根据返回的ContainerGroupId，确认业务容器和logtail容器的日志正常输出。

   ![3333](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5464031161/p230406.png)
   **注意**

   pod-stdlog卷中的标准输出日志是ECI底层组件记录的，其格式与原生K8S是一致的，会在每行标准输出日志前增加前缀，配置日志格式解析时需要考虑该前缀，可以参考用户[用户日志JSON解析]()。
   

2. 登录[日志服务控制台](https://sls.console.aliyun.com/)。

   

3. 在 **接入数据** 区域，单击 **正则-文本日志** 。

   本文以采集正则-文本文件为例。更多信息，请参见[采集文本日志](/intl.zh-CN/数据采集/Logtail采集/采集文本日志/概述.md)。
   

4. 选择日志空间，单击 **下一步** 。

   请选择Project和Logstore，您也可以直接单击立即创建准备流程。

   如果您是通过日志库下的 **数据接入** 后的加号进入采集配置流程，系统会直接跳过该步骤。
   

5. 创建机器组。如果您已有可用的机器组 ，可直接单击 **使用现有机器组** 。

   a. 确认已创建机器组，单击 **确认** **安装完毕** 。

   ![1p229932](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5464031161/p230640.png)

   b. 在 **创建机器组** 页签中，配置相关参数，单击 **下一步** 。机器组标识选择 **用户自定义标识** ，将CreateContainerGroup时配置的环境变量ALIYUN_LOGTAIL_USER_DEFINED_ID填入 **用户自定义标识** 框中。

   ![2p229933](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5464031161/p230641.png)
   

6. 配置机器组。

   选择一个机器组，将该机器组从源机器组移动到应用机器组。
   

7. 设置Logtail配置。

   Nginx访问日志、分隔符日志、JSON日志、正则日志等格式。更多信息，请参见[采集文本日志](/intl.zh-CN/数据采集/Logtail采集/采集文本日志/概述.md)。

   文本日志的采集配置如下：

   ![71](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5464031161/p230642.png)

   标准输出的配置方式类似：

   ![72](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5464031161/p230643.png)
   

8. 查询分析配置。

   默认已设置索引，您也可以根据业务需求，重新设置索引。具体操作，请参见[配置索引](/intl.zh-CN/查询与分析/配置索引.md)。

   ![81](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5464031161/p230646.png)
   

9. 完成以上步骤后，日志服务开始采集日志。

   访问[日志服务控制台](https://sls.console.aliyun.com/)，查看已经配置的收集日志。可以看到日志记录中的ip、hostname与控制台保持一致。

   ![9](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5464031161/p230647.png)
   



