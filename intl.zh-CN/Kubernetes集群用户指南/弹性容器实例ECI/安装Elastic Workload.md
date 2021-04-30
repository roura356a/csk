# 安装Elastic Workload

弹性负载会监听原始负载，并根据弹性单元设定的调度策略，克隆并生成弹性单元的负载。根据弹性负载中副本的变化，动态的分配原始负载和弹性单元上面的副本数目。本文介绍如何安装以及使用Elastic Workload（弹性负载）。

-   您已经成功创建一个Kubernetes集群，请参见[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
-   如果创建的是ACK集群，您还需要部署ack-virtual-node（请使用v2.0.0.102-045a06eb4-aliyun及以上版本），请参见[容器服务（ACK）使用ECI]()。

## 部署ack-kubernetes-elastic-workload

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，选择**市场** \> **应用目录**。

3.  在**应用目录**页面单击**阿里云应用**页签，然后选中**ack-kubernetes-elastic-workload**应用。

    **阿里云应用**包含较多应用，您可在页面右上角搜索**ack-kubernetes-elastic-workload**，支持关键字搜索。

4.  在**应用目录 - ack-kubernetes-elastic-workload**页面右侧的**创建**区域，选择目标集群，单击**创建**。

    ![创建](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8106659951/p132495.png)

5.  在控制台左侧导航栏中，单击**集群**。

6.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

7.  在集群管理页左侧导航栏中，选择**应用** \> **Helm**，可以找到ack-kubernetes-elastic-workload的应用。


## 使用elastic-workload

在Kubernetes中，任何一种负载都要解决两个问题，一个是调度问题，一个是生命周期管理问题。要实现上面描述的场景，我们核心要解决以下两个问题：

-   当副本数目到达某个数值后，如何控制调度策略的变化。
-   在生命周期管理时，如何优先处理某些Pod。

针对上面的问题介绍弹性负载的使用方式与解决方法。

使用模板创建一个简单的Deployment类型应用。

```
apiVersion: apps/v1 # for versions before 1.8.0 use apps/v1beta1
kind: Deployment
metadata:
  name: nginx-deployment-basic
  labels:
    app: nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
    #  nodeSelector:
    #    env: test-team
      containers:
      - name: nginx
        image: nginx:1.7.9 
        ports:
        - containerPort: 80
```

使用弹性负载表达Deployment类型应用。

```
# 弹性负载定义。
apiVersion: autoscaling.alibabacloud.com/v1beta1
kind: ElasticWorkload
metadata:
  name: elasticworkload-sample
spec:
  sourceTarget:
    name: nginx-deployment-basic
    kind: Deployment
    apiVersion: apps/v1
    min: 2
    max: 4
  replicas: 6
  elasticUnit:
  - name: virtual-kubelet
    labels:
      alibabacloud.com/eci: "true"
    # min: 0 每个单元也可以指定自己的上下限。
    # max: 10
```

以上就是这个场景的弹性负载定义，弹性负载的使用方式与HPA类似，通过外部挂载的方式使用，对原有的业务无侵入。

一个典型的弹性负载主要分为两个部分：

1.  SourceTarget部分主要定义原始负载的类型、副本数目可变化的范围。
2.  elasticUnit部分是一个数组，定义弹性单元的调度策略，如果有多个弹性单元，则按照模板的顺序定义。

在上面的例子中，SourceTarget的副本上下限位2~4，表示当ElasticWorkload的replicas为2~4个副本时，会分配到sourceTarget，当超过4个副本时，会分配到弹性单元virtual-kubelet，而在弹性单元virtual-kubelet中可以定义这个单元所独有的调度策略，包含label、annotation、nodeSelector、affinity、toleration等。

![调度](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8106659951/p132512.png)

弹性负载会监听原始负载，并根据弹性单元设定的调度策略，克隆并生成弹性单元的负载。根据弹性负载中副本的变化，动态的分配原始负载和弹性单元上面的副本数目。

执行弹性负载模板后，您可以通过命令行查看当前的状态，其中status中的每个单元的Desired Replicas表示弹性负载的分配副本数目。

执行以下命令查看弹性负载状态。

```
kubectl describe ew elasticworkload-sample   # same as kubectl get elasticworkload
```

预期输出：

```
Name:         elasticworkload-sample
Namespace:    default
Labels:       <none>
Annotations:  <none>
API Version:  autoscaling.alibabacloud.com/v1beta1
Kind:         ElasticWorkload
Metadata:
  Creation Timestamp:  2021-04-30T06:58:03Z
  Generation:          2
  Resource Version:  1126368056
  Self Link:         /apis/autoscaling.alibabacloud.com/v1beta1/namespaces/default/elasticworkloads/elasticworkload-sample
  UID:               a465de9e-1253-4cbe-8cd5-98393393e990
Spec:
  Elastic Unit:
    Labels:
      alibabacloud.com/eci:  true
    Name:                    virtual-kubelet
  Replicas:                  6
  Source Target:
    API Version:  apps/v1
    Kind:         Deployment
    Max:          4
    Min:          2
    Name:         nginx-deployment-basic
Status:
  Elastic Units Status:
    Desired Replicas:  2
    Name:              nginx-deployment-basic-unit-virtual-kubelet
    Update Timestamp:  2021-04-30T06:58:03Z
  Replicas:            6
  Selector:            app=nginx
  Source Target:
    API Version:       apps/v1
    Desired Replicas:  4
    Kind:              Deployment
    Name:              nginx-deployment-basic
    Update Timestamp:  2021-04-30T06:58:03Z
Events:
  Type    Reason                 Age                  From             Message
  ----    ------                 ----                 ----             -------
  Normal  UnitCreation           110s                 ElasticWorkload  ElasticWorkloadUnit nginx-deployment-basic-unit-virtual-kubelet created
  Normal  ElasticWorkloadUpdate  110s (x2 over 110s)  ElasticWorkload  ElasticWorkload update
  Normal  UnitUpdate             110s                 ElasticWorkload  ElasticWorkloadUnit virtual-kubelet has been updated
```

当下发弹性负载的模板后，可以查看Pod的情况。可以发现弹性负载克隆出了新的Deployment与Pod，并且Deployment的Pod副本数目是根据上述的规则进行动态分配的。

执行以下命令查看Pod详情。

```
kubectl get pod -o wide
```

预期输出：

```
NAME                                                           READY   STATUS    RESTARTS   AGE   IP              NODE                            NOMINATED NODE   READINESS GATES
nginx-deployment-basic-769f84b5cf-hdmlw                        1/1     Running   0          46m   172.26.240.69   cn-hangzhou.10.1.84.111         <none>           <none>
nginx-deployment-basic-769f84b5cf-lmd99                        1/1     Running   0          46m   172.26.240.6    cn-hangzhou.10.1.84.112         <none>           <none>
nginx-deployment-basic-769f84b5cf-nbp5c                        1/1     Running   0          30m   172.26.240.75   cn-hangzhou.10.1.84.111         <none>           <none>
nginx-deployment-basic-769f84b5cf-scj68                        1/1     Running   0          30m   172.26.240.11   cn-hangzhou.10.1.84.112         <none>           <none>
nginx-deployment-basic-unit-virtual-kubelet-594f86b5c9-8z876   1/1     Running   0          10m   10.1.84.119     virtual-kubelet-cn-hangzhou-i   <none>           <none>
nginx-deployment-basic-unit-virtual-kubelet-594f86b5c9-drxvq   1/1     Running   0          10m   10.1.84.118     virtual-kubelet-cn-hangzhou-i   <none>           <none>
```

此外，弹性负载也支持与HPA配合使用，可以将HPA作用在弹性负载上，如上图，弹性负载会根据HPA的状态动态调整每个单元的副本分布，例如如果当前是从6个副本缩容到4个副本，那么会优先将弹性单元的副本进行缩容。

```
apiVersion: autoscaling/v2beta2
kind: HorizontalPodAutoscaler
metadata:
  name: elastic-workload-demo
  namespace: default
spec:
  scaleTargetRef:
    apiVersion: autoscaling.alibabacloud.com/v1beta1
    kind: ElasticWorkload
    name: elasticworkload-sample
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 50
```

**说明：** 弹性负载一方面通过克隆和覆写调度策略的方式生成多个Deployment，实现了调度策略的管理，另一方面通过上层的副本计算，调整原始负载和弹性单元的副本分配，实现了针对一部分Pod的优先处理。

