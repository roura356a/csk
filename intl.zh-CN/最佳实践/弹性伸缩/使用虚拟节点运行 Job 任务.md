# 使用虚拟节点运行 Job 任务 {#concept_778452 .concept}

本文主要为您介绍通过虚拟节点运行 Job 任务的方式，您可以用最小的运维成本（无需调整节点数量），来应对集群计算资源高峰压力。

## 前提条件 {#section_kk4_o61_cz5 .section}

-   您已经创建了一个 Kubernetes 托管版集群。请参见[创建Kubernetes 托管版集群](../../../../intl.zh-CN/用户指南/Kubernetes集群/集群管理/创建Kubernetes 托管版集群.md#)。
-   您已经在该集群上部署了一个虚拟节点。请参见[虚拟节点](../../../../intl.zh-CN/用户指南/Kubernetes集群/弹性伸缩/虚拟节点.md#)。
-   您已经给 namespace vk 打上virtual-node-affinity-injection: enabled标签。请参见[通过配置namespace标签的方式创建Pod](../../../../intl.zh-CN/用户指南/Kubernetes集群/弹性伸缩/虚拟节点.md#li_8tb_qv1_yws)。

## 背景信息 {#section_5sa_45x_mz8 .section}

Kuberntes 集群的节点资源配置不足会导致 pod 无法及时运行，购买过多的节点又会导致资源的闲置浪费。而虚拟节点就像是kubernetes集群的“魔法口袋”，让我们摆脱节点计算力不足的烦扰，也避免了节点的闲置浪费，满足**无限**计算力的想象，pod 按需创建，轻松应对计算的波峰波谷。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/630230/156143155749860_zh-CN.png)

本例中，创建的 Kubernetes 托管版集群中，Master节点被托管（用户无需付费），2 台 Worker 分别是 4c8g 规格，所以整个 Kubernetes 托管版集群的可用计算容量是 8c16g。需要运行一个 Job 离线任务做数据处理，Job 的 cpu/mem 需求为 16c32g。此时 Job 无法在您创建的 Kubernetes 托管版集群中运行，因为其资源需求已经超过 Kubernetes 托管版集群的 8c16g 计算容量。因此，您可以选择把这个 Job 任务调度到虚拟节点上，而不占用集群本身节点的计算资源。

## 操作步骤 {#section_nqa_xsq_764 .section}

1.  [通过 kubectl 连接 Kubernetes 集群](../../../../intl.zh-CN/用户指南/Kubernetes集群/集群管理/通过 kubectl 连接 Kubernetes 集群.md#)。
2.  创建并拷贝以下内容到job.yaml，并执行`kubectl -n vk apply -f job.yaml`命令，部署一个job 任务。

    ``` {#codeblock_xxx_elx_0hs}
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

3.  执行如下命令，查看Pod的运行状态。

    ``` {#codeblock_0c3_hu3_z9g}
    #kubectl -n vk get pod -a
    NAME       READY     STATUS      RESTARTS   AGE
    pi-7cmwv   0/1       Completed   0          2m
    
    #kubectl -n vk describe pod
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

    **说明：** 虚拟节点中的 pod 完全是按需收费，当 pod 执行完成后就会停止收费。


可以看到，使用虚拟节点运行 Job 短时间任务可以节省计算成本，减轻集群运维负担，用户无需关心集群的计算资源是否充足，也无需关心节点的扩容和缩容。

