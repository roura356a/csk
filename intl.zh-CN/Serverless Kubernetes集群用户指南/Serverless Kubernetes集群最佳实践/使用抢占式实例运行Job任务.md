使用抢占式实例运行Job任务 
===================================

抢占式ECI实例特别适合于Job任务或者无状态应用，可以有效地节约实例使用成本。本文介绍如何使用抢占式ECI实例来运行Job任务。

背景信息 
-------------------------

使用抢占式实例前，请了解以下相关信息：

* 抢占式实例的市场价格随供需变化而浮动，您需要在创建实例时指定出价模式，当市场价格低于出价且资源库存充足时，就能成功创建抢占式实例。

  

* 抢占式实例创建成功后有一个小时的保护期，超出保护期后，如果出价低于市场价格或者资源库存不足，实例会被释放。在释放前三分钟左右，ECI会通过Kubernetes Events事件通知的方式告知您。在此期间，您可以做一定的处理来确保业务不受实例释放所影响（如切断该实例的业务入口流量）。

  

* 抢占式实例由于竞价失败而释放后数据会随之一起释放，但实例信息会保留（状态变更为Failed，失败原因为BidFailed）。如果您有重要数据需要保留，建议配置持久化存储，如云盘，NAS等。

  




更多信息，请参见[抢占式实例概述](/intl.zh-CN/实例/选择实例购买方式/抢占式实例/抢占式实例概述.md)和[创建抢占式实例]()。

配置方法 
-------------------------

您可以在Pod的声明中设置Annotation来指定创建抢占式ECI实例。相关配置项如下：

* k8s.aliyun.com/eci-spot-strategy：设置抢占式实例的出价策略。取值如下：

  * SpotAsPriceGo：系统自动出价，跟随当前市场实际价格。

    
  
  * SpotWithPriceLimit：自定义设置抢占实例价格上限。

    
  

  

* k8s.aliyun.com/eci-spot-price-limit：设置抢占式实例的每小时价格上限，最多支持精确到小数点后三位。当k8s.aliyun.com/eci-spot-strategy配置为SpotWithPriceLimit时必须设置。

  



**说明**

创建抢占式ECI实例时，建议您设置k8s.aliyun.com/eci-use-specs来指定多个规格，可以有效提升实例创建的成功率。

配置示例如下：

* SpotAsPriceGo

  该模式下，系统跟随当前市场价格自动出价。

      apiVersion: v1
      kind: Pod
      metadata:
        name: spot-as-price-go
        annotations:
          k8s.aliyun.com/eci-use-specs: ecs.sn1ne.large,2-4Gi
          # SpotAsPriceGo不需要设置价格上限；系统自动出价，跟随当前市场实际价格
          k8s.aliyun.com/eci-spot-strategy: SpotAsPriceGo

  

* SpotWithPriceLimit

  该模式下，系统根据您设置的价格上限来出价，可能会出现以下几种情况：
  * 出价＜当前市场价格：实例会处于Pending状态，并每5分钟自动进行一次出价，直到出价等于或高于市场价格时，开始自动创建实例。

    
  
  * 出价≥当前市场价格：如果库存充足，则自动创建实例。

    
  

  

      apiVersion: v1
      kind: Pod
      metadata:
        name: spot-with-price-limit
        annotations:
          k8s.aliyun.com/eci-use-specs: ecs.sn1ne.large
          k8s.aliyun.com/eci-spot-strategy: SpotWithPriceLimit
          # SpotWithPriceLimit模式必须设置价格上限，如果设置的出价高于ECI实例当前的按量价格，使用按量价格来创建实例。
          k8s.aliyun.com/eci-spot-price-limit: "0.05"

  




配置示例 
-------------------------

在Kubernetes中，Job负责批量处理短暂的一次性任务。使用抢占式实例运行Job任务的配置示例如下：

1. 准备Job的配置文件，命名为spot_job.yaml。

       apiVersion: batch/v1
       kind: Job
       metadata:
         name: pi
       spec:
         template:
           metadata:
             annotations:
               k8s.aliyun.com/eci-use-specs: ecs.t5-c1m2.large,2-4Gi
               k8s.aliyun.com/eci-spot-strategy: SpotAsPriceGo    #系统自动出价，跟随当前市场实际价格
           spec:
             containers:
             - name: pi
               image: registry-vpc.cn-beijing.aliyuncs.com/ostest/perl
               command: ["perl",  "-Mbignum=bpi", "-wle", "print bpi(2000)"]
             restartPolicy: Never

   

2. 创建Job。

       kubectl create -f spot_job.yaml

   

3. 查看运行情况。

       kubectl get pod

   

   返回信息中可以看到对应Pod已经成功创建，示例如下：

       NAME       READY   STATUS      RESTARTS   AGE
       pi-frmr8   1/1     Running     0          5s

   




抢占式实例创建成功后，在一个小时的保护期内，即使市场价格高于出价，抢占式实例也不会被释放，您可以在该实例上正常运行业务。超出保护期后，当出价低于市场价格或者资源库存不足，实例将被释放。释放有以下几种方式通知方式：

* 释放前事件通知

  抢占式实例在释放前三分钟左右，会产生释放事件，ECI会将释放事件同步到Kubernetes Events中，您可以通过以下命令查看：
  * 查看Pod详细信息

        kubectl describe pod pi-frmr8

    

    返回信息的Events中可以看到释放事件。示例如下：

        Events:
          Type     Reason            Age    From          Message
          ----     ------            ----   ----          -------
          Warning  SpotToBeReleased  3m32s  kubelet, eci  Spot ECI will be released in 3 minutes

    
  
  * 查看事件信息

        kukubectl get events

    

    返回信息中可以看到释放事件。示例如下：

        LAST SEEN   TYPE      REASON             OBJECT         MESSAGE
        3m39s       Warning   SpotToBeReleased   pod/pi-frmr8   Spot ECI will be released in 3 minutes

    
  

  

* 释放后状态展示

  抢占式实例释放后，实例信息仍会保留，状态变更为Failed，Failed原因为BidFailed。您可以通过以下命令查看：
  * 查看Pod信息

        kubectl get pod

    

    返回信息中可以看到Pod的STATUS为BidFailed。示例如下：

        NAME       READY   STATUS      RESTARTS   AGE
        pi-frmr8   1/1     BidFailed   0          3h5m

    
  
  * 查看Pod详细信息

        kubectl describe pod pi-frmr8

    

    返回信息中可以看到Pod的Status为Failed，Reason为BidFailed。示例如下：

        Status:             Failed
        Reason:             BidFailed
        Message:            The pod is spot instance, and have been released at 2020-04-08T12:36Z

    
  

  




实例释放后，Kubernetes中的Job Controller会自动创建新的实例来继续运行任务。您可以通过`kubectl get pod`命令查看Pod，返回示例如下：

    NAME       READY   STATUS      RESTARTS   AGE
    pi-frmr8   1/1     BidFailed   0          4h53m
    pi-kp5zx   1/1     Running     0          3h45m


