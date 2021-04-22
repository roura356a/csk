为Pod配置时区 
=============================

本文主要介绍如何为运行在virtual kubelet上的Pod配置不同的时区，当您使用Pod部署应用时，如果需要Pod能指定不同地点的时区，您可以参考此文档进行配置。 

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

1. 创建一个configmap，导入您需要制定的时区。

   其他时区请使用/usr/share/zoneinfo/Asia/目录下对应的文件，以下为示例：

       kubectl create configmap tz --from-file=/usr/share/zoneinfo/Asia/Shanghai

   

2. 创建配置时区的yaml文件。

       vim set-timezone.yaml

   

   将configmap挂载到/etc/localtime/Shanghai目录下，以下为yaml文件示例：

       apiVersion: v1
       kind: Pod
       metadata:
         name: timezone
       spec:
         containers:
         - name: timezone
           image: registry-vpc.cn-beijing.aliyuncs.com/eci_open/busybox:1.30
           command: [ "sleep", "10000" ]
           volumeMounts:
             - name: tz
               mountPath: /etc/localtime
               subPath: Shanghai
         volumes:
           - name: tz
             configMap:
               name: tz
         nodeSelector:
           type: virtual-kubelet
         tolerations:
         - key: virtual-kubelet.io/provider
           operator: Exists

   

3. 将yaml文件中的配置应用到Pod。

       kubectl apply -f set-timezone.yaml

   




验证结果 
-------------------------

登录到容器，验证时区是否设置成功。

1. 获取Pod信息。

       kubectl get pod/timezone

   

   返回示例如下：

       NAME         READY   STATUS    RESTARTS   AGE
       timezone     1/1     Running   0          7m20s

   

2. 进入容器。

       kubectl exec timezone -it -- sh

   

3. 查询容器的时区。

       date -R

   

   如果返回的时间与设置的时区信息对应，则表示设置成功。返回示例如下：

       Fri, 01 May 2020 10:00:11 +0800

   



