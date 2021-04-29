---
keyword: [资源调度, CPU拓扑感知调度, Kube-scheduler, cpu manager]
---

# CPU拓扑感知调度

ACK基于新版的Scheduling framework实现CPU拓扑感知调度，针对CPU敏感型的工作负载提供更好的性能。本文介绍如何使用CPU拓扑感知调度。

-   您已创建ACK Pro版集群。具体步骤，请参见[创建ACK Pro版集群](/cn.zh-CN/Kubernetes集群用户指南/ACK Pro集群/创建ACK Pro版集群.md)。

    **说明：** 目前CPU拓扑感知调度仅支持ACK Pro托管版集群。如果您需要专有版集群，请[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)申请白名单。

-   在使用CPU拓扑感知调度前，需要预先部署组件resource-controller。具体步骤，请参见[管理组件](/cn.zh-CN/Kubernetes集群用户指南/集群/升级集群/管理组件.md)。
-   系统组件版本要求具体如下表所示。

    |组件|版本要求|
    |--|----|
    |Kubernetes|1.16及以上|
    |Helm版本|3.0及以上版本|
    |Docker版本|19.03.5|
    |操作系统|CentOS 7.6、CentOS 7.7、Ubuntu 16.04、Ubuntu 18.04、Alibaba Cloud Linux 2|


Kubernetes的Node节点会运行多个Pod，其中会有部分的Pod属于CPU密集型的工作负载。在这种情况下，Pod之间会争抢节点的CPU资源。当争抢剧烈的时候，Pod会在不同的CPU Core之间进行频繁的切换，更糟糕的是在NUMA Node之间的切换。这种大量的上下文切换，会影响程序运行的性能。Kubernetes虽然有CPU Manager解决方案处理以上问题，但是因为CPU Manager特性是节点级别的CPU调度选择，所以无法在集群维度中选择最优的CPU Core组合。同时CPU Manager特性要求Pod是Guaranteed时（Pod中的每个容器必须指定CPU请求和CPU限制，并且两者要相等）才能生效，且无法适用于所有类型的Pod。

在以下场景中，建议使用CPU拓扑感知调度：

-   工作负载为计算密集型。
-   应用程序对CPU敏感。
-   运行在神龙裸金属等多核机器上。

## 使用CPU拓扑感知调度

激活CPU拓扑感知调度前，需要在提交Pod时设置annotations和Containers的值。满足以下条件：

-   在annotations处，设置cpuset-scheduler为true，激活CPU拓扑感知调度。
-   需要设置Containers的resources.limit.cpu值，且该值为整数核。

在Pod中使用CPU拓扑感知调度的模板如下。

```
apiVersion: v1
kind: Pod
metadata:
  name: cal-pi
  annotations: 
    cpuset-scheduler: 'true' #添加注解，表示CPU拓扑感知调度。
spec:
  restartPolicy: Never
  containers:
  - image: registry.cn-zhangjiakou.aliyuncs.com/xianlu/java-pi
    name: cal-pi
    resources:
      requests:
        cpu: 4
      limits:
        cpu: 4  #需要设置resources.limit.cpu值。
    env:
    - name: limit
      value: "20000"
    - name: threadNum
      value: "3000"
```

在Deployment中使用CPU拓扑感知调度的模板如下。

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: go-demo
spec:
  replicas: 4
  selector:
    matchLabels:
      app: go-demo
  template:
    metadata:
      annotations:
        cpuset-scheduler: "true" #添加注解，表示CPU拓扑感知调度。
      labels:
        app: go-demo
    spec:
      containers:
      - name: go-demo
        image: registry.cn-hangzhou.aliyuncs.com/haoshuwei24/go-demo:1k
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
        resources:
          requests:
            cpu: 1
          limits: 
            cpu: 4  #需要设置resources.limit.cpu值。
```

