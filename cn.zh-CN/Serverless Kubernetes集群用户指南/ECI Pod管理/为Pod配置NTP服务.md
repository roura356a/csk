为Pod配置NTP服务 
================================

本文主要介绍如何为运行在virtual kubelet上的Pod配置NTP服务。当您在部署应用时，如果需要Pod内的容器能与NTP服务进行时间同步，您可以参考本文进行配置。

前提条件 
-------------------------

已将virtual-kubelet升级到最新版本。

背景信息 
-------------------------

不同类型的kubernetes集群升级virtual kubelet到最新版本的方式如下：

* Serverless kubernetes：由管理员统一负责升级。

  

* 托管版kubernetes：您需要自行升级。

  

* 专有版kubernetes：您需要自行升级。

  

* 自建kubernetes：您需要自行升级。

  




操作步骤 
-------------------------

您需要在Pod的annotations中增加`k8s.aliyun.com/eci-ntp-server`注解，设置需要配置的NTP服务的IP地址。

1. 创建配置NTP服务的yaml文件。

       vim set-ntp-pod.yaml

   

   以下为yaml文件的内容示例：

       apiVersion: v1
       kind: Pod
       metadata:
         annotations:
           k8s.aliyun.com/eci-ntp-server: 10.10.5.1  # 设置您的NTP服务的IP地址
         name: set-custom-ntp
       spec:
         nodeName: virtual-kubelet
         containers:
           - image: centos:latest
             command:
               - sleep
               - "3600"
             imagePullPolicy: IfNotPresent
             name: centos

   

2. 将yaml文件中的配置应用到Pod。

       kubectl apply -f set-ntp-pod.yaml

   




验证结果 
-------------------------

登录到容器，验证NTP服务是否设置成功。

1. 获取Pod信息。

       kubectl get pod/set-custom-ntp

   

   返回示例如下：

       NAME                  READY   STATUS    RESTARTS   AGE
       set-custom-ntp   1/1         Running   0                 7m20s

   

2. 进入容器。

       kubectl exec set-custom-ntp -it -- bash

   

3. 查询容器的时间来源。

       chronyc sources

   

   如果返回了NTP服务的IP地址，则表示设置成功。返回示例如下：

       210 Number of sources = 1
       MS Name/IP address         Stratum Poll Reach LastRx Last                            sample
       ===============================================================================
       ^* 10.10.5.1                    2           6     377     35       +40us[ +135us] +/-   14ms 

   



