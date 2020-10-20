# 抢占式实例运行Job任务

本文主要演示如何在阿里云容器服务Serverless Kubernetes上运行一个Job类型任务，以此来说明如何在Serverless Kubernetes使用ECI抢占式实例（也称Spot实例）。

## 背景

抢占式实例（Spot实例）可以有效节省用户使用ECI的成本，特别适合于Job短时任务或者无状态类型的业务场景。

1.  抢占式实例的实际价格随供需会波动，相对于按量付费实例价格有一定的折扣。
2.  在实例创建成功后会有一个小时的保护周期，超过保护周期后，可能随时被ECI释放，在实例被释放前3分钟左右，ECI会通过Kubernetes events事件通知的方式告知用户，用户可以在此期间做一定的处理来确保业务不受实例释放所影响（比如切断该pod的业务入口流量）。
3.  实例由于竞价失败而被释放后数据会一起释放，但实例信息会依然保留，pod状态会置为Failed，原因是BidFailed（竞价失败）。如果需要保留重要数据，建议您使用持久化的存储产品，比如EBS云盘或者NAS存储。

更具体的信息，请参见[抢占式实例（Spot）]()和[计费概述]()。

## 如何使用抢占式实例

您可以在创建Pod时，在Pod声明时设置Annotation来指定创建抢占式实例：

|Name|Name|Sample|Description|
|----|----|------|-----------|
|k8s.aliyun.com/eci-spot-strategy|string|SpotAsPriceGo|**SpotAsPriceGo:**系统自动出价，跟随当前市场实际价格**SpotWithPriceLimit:** 设置抢占实例价格上限|
|k8s.aliyun.com/eci-spot-price-limit|float|"0.250"|只有k8s.aliyun.com/eci-spot-strategy设置为SpotWithPriceLimit时有效。设置实例每小时最高价格，支持最大 3 位小数|

**说明：**

您可以在创建抢占式ECI实例时，通过Annotation `k8s.aliyun.com/eci-use-specs`来设置一个或**多个实例规格**，可以有效提升实例创建的成功率，具体请参见[ECI Pod Annotation]()。

## SpotAsPriceGo

系统自动跟随市场实际价格来出价。

```
apiVersion: v1
kind: Pod
metadata:
  name: spot-as-price-go
  annotations:
    k8s.aliyun.com/eci-use-specs: ecs.sn1ne.large,2-4Gi
    # SpotAsPriceGo不需要设置价格上限；系统自动出价，跟随当前市场实际价格
    k8s.aliyun.com/eci-spot-strategy: SpotAsPriceGo
```

## SpotWithPriceLimit

系统根据用户设置的价格上限来出价，这里会出现几种情况：

1.  用户价格 < Spot市场价格，实例会处于pending状态，并每5分钟自动进行一次出价，当价格等于或高于市场价格时，开始自动创建实例。运行一小时后，市场价格如果高于用户价格，则实例随时可能会被释放。
2.  用户价格 \>= Spot市场价格，如果库存充足则自动创建实例，按成功创建实例时的市场价格来计价，默认市场价格为小时价，将小时价除以3600即可得到每秒的价格。抢占式实例按秒计费。

```
apiVersion: v1
kind: Pod
metadata:
  name: spot-with-price-limit
  annotations:
    k8s.aliyun.com/eci-use-specs: ecs.sn1ne.large
    k8s.aliyun.com/eci-spot-strategy: SpotWithPriceLimit
    # SpotWithPriceLimit方式，必须设置价格上限
    # 如果用户出价高于ECI当前的按量价格，使用ECI按量实例价格来创建实例。
    k8s.aliyun.com/eci-spot-price-limit: "0.05"
```

## 示例

使用Job创建抢占式实例，并指定跟随市场实际价格来出价。

```
apiVersion: batch/v1
kind: Job
metadata:
  name: pi
spec:
  template:
    metadata:
      annotations:
        k8s.aliyun.com/eci-use-specs: ecs.t5-c1m2.large,2-4Gi
        k8s.aliyun.com/eci-spot-strategy: SpotAsPriceGo
    spec:
      containers:
      - name: pi
        image: registry-vpc.cn-beijing.aliyuncs.com/ostest/perl
        command: ["perl",  "-Mbignum=bpi", "-wle", "print bpi(2000)"]
      restartPolicy: Never
```

提交任务：

```
kubectl create -f spot_job.yaml
kubectl get po
```

```
NAME       READY   STATUS      RESTARTS   AGE
pi-frmr8   1/1     Running     0          5s
```

## 释放通知

创建的抢占式Pod实例，拥有一小时的保护周期，即在创建成功后第一个小时内，即使市场价格浮动超过了出价，抢占式实例也不会被释放，您可以在该抢占式实例上正常运行业务。超过保护周期后，当用户设置价格小于市场价格或者资源库存不足时，会触发实例被释放，释放有两种通知方式：

-   释放前事件通知：抢占实例在释放前 **3分钟**会产生释放事件，ECI会将释放事件同步到Kubernetes events列表中，您可以通过`kubectl describe pod ${pod_name}`查看。

```
kubectl describe po pi-frmr8
```

```
......
Events:
  Type     Reason            Age    From          Message
  ----     ------            ----   ----          -------
  Warning  SpotToBeReleased  3m32s  kubelet, eci  Spot ECI will be released in 3 minutes
```

```
kubectl get events
```

```
LAST SEEN   TYPE      REASON             OBJECT         MESSAGE
3m39s       Warning   SpotToBeReleased   pod/pi-frmr8   Spot ECI will be released in 3 minutes
```

-   释放后状态展示：抢占实例释放后ECI会修改Pod状态为`Failed`，Failed原因是`BidFailed`。

```
kubectl get po
```

```
NAME       READY   STATUS      RESTARTS   AGE
pi-frmr8   1/1     BidFailed   0          3h5m
```

```
kubectl describe po pi-frmr8
```

```
......
Status:             Failed
Reason:             BidFailed
Message:            The pod is spot instance, and have been released at 2020-04-08T12:36Z
......
```

实例被释放后，K8s中的Job controller或者Deployment controller会自动新建实例来继续运行任务：

```
kubectl get po
```

```
NAME       READY   STATUS      RESTARTS   AGE
pi-frmr8   1/1     BidFailed   0          4h53m
pi-kp5zx   1/1     Running     0          3h45m
```

