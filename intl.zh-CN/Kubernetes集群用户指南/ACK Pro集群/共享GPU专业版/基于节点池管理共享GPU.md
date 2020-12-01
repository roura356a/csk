---
keyword: [节点池, 显存隔离, 特定标签]
---

# 基于节点池管理共享GPU

ACK Pro集群支持通过节点池管理共享GPU的调度能力和显存隔离能力。本文介绍如何基于节点池管理共享GPU。

-   Helm版本≥3.0.0。
-   创建ACK Pro集群。

    创建ACK Pro集群时，需要按以下要求设置Kubernetes版本和实例规格类型，其他配置请参见[创建Kubernetes Pro版集群](/intl.zh-CN/Kubernetes集群用户指南/ACK Pro集群/创建Kubernetes Pro版集群.md)。

    **说明：** 仅支持在ACK Pro集群安装共享GPU组件，如果您使用的是专有版集群，您可以[提交工单](https://workorder-intl.console.aliyun.com/console.htm)申请白名单。

    -   Kubernetes版本≥1.18.8。
    -   设置实例规格类型为**异构计算GPU/FPGA/NPU**。
-   目前只有以下地域支持GPU显存隔离功能。如果您需要使用该功能，请确保集群所在地域在此范围内。

    |地域|地域ID|
    |--|----|
    |华北2（北京）|cn-beijing|
    |华东2（上海）|cn-shanghai|
    |华东1（杭州）|cn-hangzhou|
    |华北（张家口）|cn-zhangjiakou|
    |华南1（深圳）|cn-shenzhen|
    |西南1（成都）|cn-chengdu|
    |华南2（河源）|cn-heyuan|
    |中国（香港）|cn-hongkong|
    |印度尼西亚（雅加达）|ap-southeast-5|
    |新加坡|ap-southeast-1|
    |美国（弗吉尼亚）|us-east-1|
    |美国（硅谷）|us-west-1|

-   规划节点池的名称、节点标签、共享调度和显存隔离能力。

    节点池名称可自定义，本文以cgpu和cgpu-no-isolation为例进行阐述。关于节点标签的详细说明请参见[GPU节点标签说明]()。

    |节点池名称|共享调度|显存隔离|节点标签|
    |-----|----|----|----|
    |cgpu-no-isolation|是|否|    -   ack.node.gpu.schedule=share
    -   ack.node.gpu.placement=binpack |
    |cgpu|是|是|    -   ack.node.gpu.schedule=cgpu
    -   ack.node.gpu.placement=binpack |


## 适用场景

在ACK上使用GPU共享调度时，您可能遇到以下场景：

-   训练任务A在代码中已配置该任务可使用的GPU显存大小，此时只需要提供GPU共享调度能力，不需要提供GPU显存隔离能力。
-   训练任务B在代码中没有配置该任务可使用的GPU显存大小，此时不仅需要提供GPU共享调度能力，还需要提供GPU显存隔离能力。

基于节点池管理共享GPU能够在一个集群支持以上两种场景。您只需要在集群中创建以下两个节点池：

-   第一个节点池的节点只提供共享调度能力，不提供显存隔离能力，该节点池只适用于运行训练任务A。
-   第二个节点池的节点同时提供共享调度能力和显存隔离能力，该节点池只适用于运行训练任务B。

## 注意事项

在使用节点池管理共享GPU的过程中，需要注意以下两点：

-   建议给任务设置nodeSelector。

    使用节点池管理共享GPU时，如果某个任务没有设置nodeSelector，那么该任务的Pod可能被调度到任意一个节点池。例如，某些Pod不需要显存隔离，结果被调度到具有显存隔离的节点。

-   如果您需要关闭显存隔离能力，需要在节点卸载cGPU的Kernel Module并重启机器后才能生效。
    -   如果特定标签由ack.node.gpu.schedule=cgpu变成ack.node.gpu.schedule=share，该节点不会关闭显存隔离能力，需要在节点卸载cGPU的Kernel Module并重启机器后才能生效。有关如何卸载cGPU请参见[使用cGPU服务隔离GPU资源]()。
    -   如果特定标签ack.node.gpu.schedule=share变成ack.node.gpu.schedule=cgpu，该节点会直接由不支持显存隔离变成支持显存隔离。

## 步骤一：安装共享GPU组件

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，选择**市场** \> **应用目录**。

3.  在应用目录页面的搜索框中搜索ack-ai-installer，找到ack-ai-installer，单击**ack-ai-installer**。

4.  在应用目录-ack-ai-installer页面创建区域选择目标集群，然后单击**创建**。

    安装ack-ai-installer成功后，将会跳转到ack-ai-installer详情页面，您可以看到ack-ai-installer下的各个组件。


## 步骤二：创建节点池

创建两个不同能力的节点池，一个节点池支持共享调度和显存隔离，另一个节点池仅支持共享调度，不支持显存隔离。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面单击已安装共享GPU组件集群**操作**列的**节点池**。

4.  在节点池页面单击右上角的**创建节点池**。

5.  在创建节点池页面，设置创建节点池的配置项。

    有关配置项的详细说明，请参见[创建Kubernetes Pro版集群](/intl.zh-CN/Kubernetes集群用户指南/ACK Pro集群/创建Kubernetes Pro版集群.md)。部分配置项说明如下：

    -   节点池名称：设置**节点池名称**为cgpu。
    -   数量：设置节点池初始节点数量。如不需要创建节点，可以填写为0。
    -   操作系统：选择节点的操作系统，包括CentOS、AliyunLinux、Windows。
    -   ECS标签：您可以为ECS实例添加标签。
    -   自定义资源组：您可以指定节点池所扩容节点的资源组信息。
    -   节点标签：为集群节点添加标签。
        -   开启GPU共享调度能力，同时开启GPU显存隔离能力。

            单击**节点标签**的![节点标签](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7405585061/p183919.png)，设置**键**为ack.node.gpu.schedule，**值**为cgpu。

        -   在节点上使用Binpack算法为Pod选择GPU卡。

            单击**节点标签**的![节点标签](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7405585061/p183919.png)，设置**键**为ack.node.gpu.placement，设置**值**为binpack。

6.  重复步骤[4](#step_ljg_wgy_9f0)和[5](#step_2r4_6w1_1g2)，创建cgpu-no-isolation节点池。

    创建cgpu-no-isolation节点池时，设置节点池名称为cgpu-no-isolation，并添加ack.node.gpu.schedule=share和ack.node.gpu.placement=binpack标签。


## 步骤三：添加GPU节点

完成创建节点池后，您还可以在节点池中添加GPU节点。添加GPU节点时，您需要将实例规格类型设置为**异构计算GPU/FPGA/NPU**。具体操作，请参见[添加已有节点](/intl.zh-CN/Kubernetes集群用户指南/节点管理/添加已有节点.md)或[扩容节点池](/intl.zh-CN/Kubernetes集群用户指南/节点管理/节点池管理/扩容节点池.md)。

**说明：** 如果您添加节点池时已经创建GPU节点，您可以跳过此步骤。

## 步骤四：提交任务

为了验证创建的节点池是否具有GPU共享调度和显存隔离的能力，需要提交两个任务：

-   cgpu-test：该任务没有设置可用的GPU显存大小，需要借助cgpu的显存隔离能力才能正常运行。
-   cgpu-test-no-isolation：该任务设置了每个GPU可用显存大小，不需要借助cgpu的显存隔离能力。

**提交cgpu-test-no-isolation任务**

1.  创建cgpu-test-no-isolation.yaml。

    ```
    apiVersion: apps/v1
    kind: StatefulSet
    
    metadata:
      name: cgpu-test-no-isolation
      labels:
        app: cgpu-test-no-isolation
    
    spec:
      replicas: 1
      serviceName: "cgpu-test-no-isolation"
      podManagementPolicy: "Parallel"
      selector: # define how the deployment finds the pods it manages
        matchLabels:
          app: cgpu-test-no-isolation
    
      template: # define the pods specifications
        metadata:
          labels:
            app: cgpu-test-no-isolation
    
        spec:
          nodeSelector:
            # 需指定该任务调度到哪些节点上。
            # 由带有标签ack.node.gpu.schedule=share的节点组成了节点池cgpu-no-isolation。
            ack.node.gpu.schedule: "share"
          containers:
          - name: cgpu-test-no-isolation
            image: cheyang/gpu-player:v2
            resources:
              limits:
                # 申请了3 GiB显存。
                aliyun.com/gpu-mem: 3
    ```

    -   aliyun.com/gpu-mem：设置GPU显存大小。
    -   nodeSelector：指定节点池为cgpu-no-isolation。
2.  提交cgpu-test-no-isolation任务。

    ```
    kubectl apply -f cgpu-test-no-isolation.yaml 
    ```


**提交cgpu-test任务**

1.  创建cgpu-test.yaml。

    ```
    apiVersion: apps/v1
    kind: StatefulSet
    
    metadata:
      name: cgpu-test
      labels:
        app: cgpu-test
    
    spec:
      replicas: 1
      serviceName: "cgpu-test"
      podManagementPolicy: "Parallel"
      selector: # define how the deployment finds the pods it manages
        matchLabels:
          app: cgpu-test
    
      template: # define the pods specifications
        metadata:
          labels:
            app: cgpu-test
    
        spec:
          nodeSelector:
            # 加node selector，选择节点池cgpu。
            ack.node.gpu.schedule: "cgpu"
          containers:
          - name: cgpu-test
            image: registry.cn-shanghai.aliyuncs.com/tensorflow-samples/tensorflow-gpu-mem:10.0-runtime-centos7
            command:
              - python3
              - /app/main.py
            env:
            resources:
              limits:
                # 申请的显存单位为GiB，该容器申请了3 GiB显存。
                aliyun.com/gpu-mem: 3
    ```

    -   aliyun.com/gpu-mem：设置GPU显存大小。
    -   nodeSelector：指定节点池为cgpu。
2.  提交cgpu-test任务。

    ```
    kubectl apply -f cgpu-test.yaml 
    ```


## 步骤五：结果验证

如果设置了显存隔离，那么容器只能看到GPU卡分配给容器的显存。如果没有设置显存隔离，那么容器可以看到整个GPU卡的显存。执行以下命令，查看容器能看到的GPU卡的显存。

-   查看任务状态。

    ```
    kubectl get po
    ```

    预期输出：

    ```
    NAME                       READY   STATUS    RESTARTS   AGE
    cgpu-test-0                1/1     Running   0          5m55s
    cgpu-test-no-isolation-0   1/1     Running   0          6m42s
    ```

-   查看cgpu-test-0容器可以看到的显存。

    ```
    kubectl exec cgpu-test-0 nvidia-smi
    ```

    预期输出：

    ```
    Mon Nov  2 11:33:10 2020
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 418.87.01    Driver Version: 418.87.01    CUDA Version: 10.1     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |===============================+======================+======================|
    |   0  Tesla V100-SXM2...  On   | 00000000:00:07.0 Off |                    0 |
    | N/A   34C    P0    54W / 300W |   3039MiB /  3226MiB |      1%      Default |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                       GPU Memory |
    |  GPU       PID   Type   Process name                             Usage      |
    |=============================================================================|
    +-----------------------------------------------------------------------------+
    ```

    cgpu-test-0容器可以看到的显存为3226 MiB（该GPU卡的总显存为16 GiB），说明cGPU的显存隔离能力生效了。

-   查看cgpu-test-no-isolation-0容器可以看到的显存。

    ```
    kubectl exec cgpu-test-no-isolation-0 nvidia-smi
    ```

    预期输出：

    ```
    Mon Nov  2 11:39:59 2020
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 418.87.01    Driver Version: 418.87.01    CUDA Version: 10.1     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |===============================+======================+======================|
    |   0  Tesla V100-SXM2...  On   | 00000000:00:07.0 Off |                    0 |
    | N/A   37C    P0    56W / 300W |   1929MiB / 16130MiB |      1%      Default |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                       GPU Memory |
    |  GPU       PID   Type   Process name                             Usage      |
    |=============================================================================|
    +-----------------------------------------------------------------------------+
    ```

    cgpu-test-no-isolation-0容器可以看到的显存为16130 MiB（该GPU卡的总显存为16 GiB），说明没有设置显存隔离。

    对比cgpu-test-no-isolation-0和cgpu-test-0容器。cgpu-test-no-isolation-0能够看到整张GPU卡的显存，而cgpu-test-0只能看到申请的GPU显存大小，说明基于节点池管理共享GPU是成功的。


