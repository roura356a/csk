---
keyword: [虚拟节点, Job任务]
---

# 基于ECI运行Job任务

本文主要为您介绍通过虚拟节点运行Job任务的方式，您可以用最小的运维成本（无需调整节点数量）来应对集群计算资源高峰压力。

Kubernetes集群的节点资源配置不足会导致Pod无法及时运行，购买过多的节点又会导致资源的闲置浪费。而虚拟节点就像是kubernetes集群的“魔法口袋”，让我们摆脱节点计算力不足的烦扰，也避免了节点的闲置浪费，满足无限计算力的想象，Pod按需创建，轻松应对计算的波峰波谷。

![Kuberntes集群](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5106659951/p49860.png)

本例中，创建的Kubernetes托管版集群中，Master节点被托管（用户无需付费），2台Worker均为4c8g规格，所以整个Kubernetes托管版集群的可用计算容量是8c16g。需要运行一个Job离线任务做数据处理，Job的cpu/mem需求为16c32g。此时Job无法在您创建的Kubernetes托管版集群中运行，因为其资源需求已经超过Kubernetes托管版集群的8c16g计算容量。因此，您可以选择把这个Job任务调度到虚拟节点上，而不占用集群本身节点的计算资源。

## 前提条件

-   您已经创建了一个Kubernetes托管版集群。具体操作，请参见[创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
-   您已经在该集群上部署了一个虚拟节点。具体操作，请参见[通过部署ACK虚拟节点组件创建ECI Pod](/cn.zh-CN/Kubernetes集群用户指南/弹性容器实例ECI/通过部署ACK虚拟节点组件创建ECI Pod.md)。
-   您已经给namespace vk打上virtual-node-affinity-injection: enabled标签。具体操作，请参见[通过配置namespace标签的方式创建Pod](/cn.zh-CN/Kubernetes集群用户指南/弹性容器实例ECI/通过部署ACK虚拟节点组件创建ECI Pod.md)。
-   通过kubectl连接Kubernetes集群。具体操作，请参见[通过kubectl连接Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl连接Kubernetes集群.md)。

## 操作步骤

1.  通过kubectl客户端创建job.yaml文件，并复制以下内容到该文件中。

    ```
    kind: Job
    metadata:
      name: pi
    spec:
      template:
        spec:
          containers:
          - name: pi
            image: perl
            command: ["perl",  "-Mbignum=bpi", "-wle", "print bpi(2000)"]
            resources:
              requests:
                cpu: 16
                memory: 32Gi
          restartPolicy: Never
      backoffLimit: 4
    ```

2.  执行以下命令部署一个job任务。

    ```
    kubectl -n vk apply -f job.yaml
    ```

3.  查看Pod的运行状态。

    执行以下命令查看Pod的状态。

    ```
    kubectl -n vk get pod -a
    ```

    预期输出：

    ```
    NAME       READY     STATUS      RESTARTS   AGE
    pi-7cmwv   0/1       Completed   0          2m
    ```

    执行以下命令查看Pod的具体运行状态。

    ```
    kubectl -n vk describe pod
    ```

    预期输出：

    ```
    Name:               pi-7cmwv
    Namespace:          vk
    Priority:           0
    PriorityClassName:  <none>
    Node:               virtual-kubelet/
    ...
    Events:
      Type    Reason                 Age   From               Message
      ----    ------                 ----  ----               -------
      Normal  Scheduled              3m    default-scheduler  Successfully assigned vk/pi-7cmwv to virtual-kubelet
      Normal  SuccessfulMountVolume  2m    kubelet, eci       MountVolume.SetUp succeeded for volume "default-token-b2tff"
      Normal  Pulling                2m    kubelet, eci       pulling image "perl"
      Normal  Pulled                 2m    kubelet, eci       Successfully pulled image "perl"
      Normal  Created                2m    kubelet, eci       Created container
      Normal  Started                2m    kubelet, eci       Started container
    ```

    **说明：** 虚拟节点中的Pod完全是按需收费，当Pod执行完成后就会停止收费。


因此，我们可以看到，使用虚拟节点运行Job短时间任务可以节省计算成本，减轻集群运维负担，用户无需关心集群的计算资源是否充足，也无需关心节点的扩容和缩容。

