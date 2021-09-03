---
keyword: [共享GPU, 托管Prometheus监控, GPU资源隔离]
---

# 监控和隔离GPU资源

阿里云容器服务Kubernetes版（ACK）通过托管的Prometheus，可以提升GPU资源管理的可见性。通过共享GPU方案，能够实现多个应用调度到同一张GPU卡上，并对卡上的每个应用实现显存隔离与算力分割。本文以实际示例介绍如何通过托管的Prometheus查看集群的GPU显存使用，以及验证共享GPU方案。

## 适用场景

本文示例适用于已开通GPU共享调度功能的专有版GPU集群及ACK Pro版集群。

## 前提条件

-   已创建GPU专有版集群，且Kubernetes版本不低于1.16。具体操作，请参见[创建专有GPU集群](/intl.zh-CN/Kubernetes集群用户指南/GPU/NPU/创建异构计算集群/创建专有GPU集群.md)。
-   已开通ARMS。具体操作，请参见[开通和升级ARMS](/intl.zh-CN/快速入门/开通和升级ARMS.md)。
-   使用阿里云账号登录[RAM控制台](https://ram.console.aliyun.com/)，为阿里云账号授权arms-prometheus功能。
-   GPU硬件为Tesla P4、Tesla P100、 Tesla T4或Tesla v100（16 GB）。

## 背景信息

推动人工智能不断向前的动力来自于强大的算力、海量的数据和优化的算法，而NVIDIA GPU是最流行的异构算力提供者，是高性能深度学习的基石。GPU的价格不菲，从使用率的角度来看，模型预测场景下，应用独占GPU模式会造成计算资源的浪费。共享GPU模式可以提升资源利用率，但需要考虑如何达到成本和QPS平衡的最优，以及如何保障应用的SLA。

## 通过托管Prometheus监控独享GPU

1.  登录[ARMS控制台](https://arms-intl.console.aliyun.com/)。

2.  在左侧导航栏中，单击**Prometheus监控**。

3.  在**Prometheus监控**页面中，选择集群所在地域，然后单击目标集群**操作**列的**安装**。

4.  在**确认**对话框中，单击**确认**。

    插件安装过程需要2分钟左右。安装插件完毕后，**已安装大盘**列中将显示全部已安装的插件。

5.  通过命令行部署以下示例应用，详情请参见[通过命令管理应用](/intl.zh-CN/Kubernetes集群用户指南/应用/工作负载/创建无状态工作负载Deployment.md)。

    ```
    apiVersion: apps/v1
    kind: StatefulSet
    metadata:
      name: app-3g-v1
      labels:
        app: app-3g-v1
    spec:
      replicas: 1
      serviceName: "app-3g-v1"
      podManagementPolicy: "Parallel"
      selector: # define how the deployment finds the pods it manages
        matchLabels:
          app: app-3g-v1
      updateStrategy:
        type: RollingUpdate
      template: # define the pods specifications
        metadata:
          labels:
            app: app-3g-v1
        spec:
          containers:
          - name: app-3g-v1
            image: registry.cn-shanghai.aliyuncs.com/tensorflow-samples/cuda-malloc:3G
            resources:
              limits:
                nvidia.com/gpu: 1
    ```

    部署成功后，执行以下命令，查看应用的状态，可以知道应用的名称是app-3g-v1-0。

    ```
    kubectl get po
    ```

    预期输出：

    ```
    NAME          READY   STATUS    RESTARTS   AGE
    app-3g-v1-0   1/1     Running   1          2m56s
    ```

6.  单击目标集群，在目标集群的**大盘列表**页面，单击**名称**列的**GPU APP**。

    可以看到该应用的GPU显存占用率仅为20%，存在80%的浪费。而它使用的显存稳定在3.4 GB左右，而总显存为16 GB左右。因此一个应用独占一张GPU卡的模式比较浪费，可以考虑通过使用cGPU（container GPU）将多个应用部署在同一个GPU卡上。

    ![GPU显存利用率](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/4875659951/p101791.png)


## 实现多容器共享同一张GPU卡

1.  为带有GPU设备的节点打标签。

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

    2.  在控制台左侧导航栏中，单击**集群**。

    3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**应用管理**。

    4.  在集群管理页左侧导航栏中，选择**节点管理** \> **节点**。

    5.  在**节点管理**页面，单击右上角**标签与污点管理**。

    6.  在**标签与污点管理**页面中，批量选择Worker节点，然后单击**添加标签**。

    7.  在**添加**对话框中，填写指定的标签名称和值（标签的名称为cgpu，值为true），单击**确定**。

        **说明：** 如果某个Worker节点设置了标签为cgpu=true，那么该节点将不再拥有独享gpu资源**nvidia.com/gpu**；如果该节点需要关闭gpu共享功能，请设置标签cgpu的值为false，同时该节点将重新拥有独享gpu资源**nvidia.com/gpu**。

2.  安装cGPU相关组件。

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

    2.  在控制台左侧导航栏中，选择**市场** \> **应用目录**。

    3.  在应用目录页面搜索ack-cgpu，然后单击ack-cgpu。

    4.  在右侧的**创建**面板中选择前提条件中创建的集群和命名空间，并单击**创建**。

    5.  登录Master节点并执行以下命令查看GPU资源。

        登录Master节点相关步骤，请参见[t16645.md\#](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl工具连接集群.md)。

        ```
        kubectl inspect cgpu
        ```

        预期输出：

        ```
        NAME                       IPADDRESS      GPU0(Allocated/Total)  GPU Memory(GiB)
        cn-hangzhou.192.168.2.167  192.168.2.167  0/15                   0/15
        ----------------------------------------------------------------------
        Allocated/Total GPU Memory In Cluster:
        0/15 (0%)
        ```

        **说明：** 此时可以发现该节点的GPU资源维度已经从GPU显卡变成GPU显存。

3.  部署共享GPU的工作负载。

    1.  修改之前部署应用的YAML文件。

        -   将实例的副本数从1改为2，这样可以指定部署两个负载。而在原有GPU独享的配置下，单个GPU卡智能调度单个容器；修改配置后，可以部署两个Pod实例。
        -   将资源维度从`nvidia.com/gpu`变为`aliyun.com/gpu-mem`，单位也从个变成了GB。
        ```
        apiVersion: apps/v1
        kind: StatefulSet
        metadata:
          name: app-3g-v1
          labels:
            app: app-3g-v1
        spec:
          replicas: 2
          serviceName: "app-3g-v1"
          podManagementPolicy: "Parallel"
          selector: # define how the deployment finds the pods it manages
            matchLabels:
              app: app-3g-v1
          template: # define the pods specifications
            metadata:
              labels:
                app: app-3g-v1
            spec:
              containers:
              - name: app-3g-v1
                image: registry.cn-shanghai.aliyuncs.com/tensorflow-samples/cuda-malloc:3G
                resources:
                  limits:
                    aliyun.com/gpu-mem: 4   #每个pod申请4 GB显存，因为replicas值为2，所以该应用总共申请8 GB显存
        ```

    2.  按照GPU显存维度调度重新创建工作负载。

        从运行结果看，两个Pod都运行在同一个GPU设备上。

        ```
        kubectl inspect cgpu -d
        ```

        预期输出：

        ```
        NAME:       cn-hangzhou.192.168.2.167
        IPADDRESS:  192.168.2.167
        
        NAME         NAMESPACE  GPU0(Allocated)
        app-3g-v1-0  default    4
        app-3g-v1-1  default    4
        Allocated :  8 (53%)
        Total :      15
        --------------------------------------------------------
        
        Allocated/Total GPU Memory In Cluster:  8/15 (53%)
        ```

    3.  执行以下命令，分别登录到两个容器。

        可以看到各自GPU显存的上限已经设置为4301 MiB， 也就是在容器内使用的GPU显存不会超过此上限。

        ```
        kubectl exec -it app-3g-v1-0 nvidia-smi
        ```

        预期输出：

        ```
        Mon Apr 13 01:33:10 2020
        +-----------------------------------------------------------------------------+
        | NVIDIA-SMI 418.87.01    Driver Version: 418.87.01    CUDA Version: 10.1     |
        |-------------------------------+----------------------+----------------------+
        | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
        | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
        |===============================+======================+======================|
        |   0  Tesla V100-SXM2...  On   | 00000000:00:07.0 Off |                    0 |
        | N/A   37C    P0    57W / 300W |   3193MiB /  4301MiB |      0%      Default |
        +-------------------------------+----------------------+----------------------+
        
        +-----------------------------------------------------------------------------+
        | Processes:                                                       GPU Memory |
        |  GPU       PID   Type   Process name                             Usage      |
        |=============================================================================|
        +-----------------------------------------------------------------------------+
        ```

        ```
        kubectl exec -it app-3g-v1-1 nvidia-smi
        ```

        预期输出：

        ```
        Mon Apr 13 01:36:07 2020
        +-----------------------------------------------------------------------------+
        | NVIDIA-SMI 418.87.01    Driver Version: 418.87.01    CUDA Version: 10.1     |
        |-------------------------------+----------------------+----------------------+
        | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
        | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
        |===============================+======================+======================|
        |   0  Tesla V100-SXM2...  On   | 00000000:00:07.0 Off |                    0 |
        | N/A   38C    P0    57W / 300W |   3193MiB /  4301MiB |      0%      Default |
        +-------------------------------+----------------------+----------------------+
        
        +-----------------------------------------------------------------------------+
        | Processes:                                                       GPU Memory |
        |  GPU       PID   Type   Process name                             Usage      |
        |=============================================================================|
        +-----------------------------------------------------------------------------+
        ```

    4.  登录到节点，查看GPU的使用情况。

        可以看到该GPU被使用的显存资源为两个容器之和，即6396 MiB，因此cGPU资源已经实现了按容器隔离的效果。如果此时登录到容器内尝试申请更多的GPU资源，会直接报出显存分配失败的错误。

        1.  执行以下命令，登录到节点。

            ```
            kubectl exec -it app-3g-v1-1 bash
            ```

        2.  执行以下命令， 查看GPU的使用情况。

            ```
            cuda_malloc -size=1024
            ```

            预期输出：

            ```
            gpu_cuda_malloc starting...
            Detected 1 CUDA Capable device(s)
            
            Device 0: "Tesla V100-SXM2-16GB"
              CUDA Driver Version / Runtime Version          10.1 / 10.1
              Total amount of global memory:                 4301 MBytes (4509925376 bytes)
            Try to malloc 1024 MBytes memory on GPU 0
            CUDA error at cgpu_cuda_malloc.cu:119 code=2(cudaErrorMemoryAllocation) "cudaMalloc( (void**)&dev_c, malloc_size)"
            ```


您可以通过[ARMS控制台](https://arms-intl.console.aliyun.com/)，从应用和节点两个维度来监控GPU的使用量。

-   GPU APP：可以查看每个应用的GPU显存用量和占比。

    ![GPU App](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/4875659951/p101827.png)

-   GPU Node：可以查看GPU卡的显存使用量。

    ![GPU node](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/4875659951/p101828.png)


## 使用托管Prometheus来监控共享GPU

当某个应用声明的GPU显存使用量超过了资源上限后，共享GPU方案中的GPU显存隔离模块可以确保其他应用不受影响。

1.  部署一个新的GPU应用。

    该应用声明使用的GPU显存是4 GB，但是它实际使用的GPU显存为6 GB。

    ```
    apiVersion: apps/v1
    kind: StatefulSet
    metadata:
      name: app-6g-v1
      labels:
        app: app-6g-v1
    spec:
      replicas: 1
      serviceName: "app-6g-v1"
      podManagementPolicy: "Parallel"
      selector: # define how the deployment finds the pods it manages
        matchLabels:
          app: app-6g-v1
      template: # define the pods specifications
        metadata:
          labels:
            app: app-6g-v1
        spec:
          containers:
          - name: app-6g-v1
            image: registry.cn-shanghai.aliyuncs.com/tensorflow-samples/cuda-malloc:6G
            resources:
              limits:
                aliyun.com/gpu-mem: 4 #每个Pod申请了4 GB显存，副本数为1，该应用总共申请4 GB显存
    ```

2.  执行以下命令，查看Pod的状态。

    新应用的pod一直处于**CrashLoopBackOff**，而之前两个Pod还是正常运行的状态。

    ```
    kubectl get pod
    ```

    预期输出：

    ```
    NAME          READY   STATUS             RESTARTS   AGE
    app-3g-v1-0   1/1     Running            0          7h35m
    app-3g-v1-1   1/1     Running            0          7h35m
    app-6g-v1-0   0/1     CrashLoopBackOff   5          3m15s
    ```

3.  执行以下命令，查看容器的日志报错。

    可以看到报错是由于cudaErrorMemoryAllocation造成的。

    ```
    kubectl logs app-6g-v1-0
    ```

    预期输出：

    ```
    CUDA error at cgpu_cuda_malloc.cu:119 code=2(cudaErrorMemoryAllocation) "cudaMalloc( (void**)&dev_c, malloc_size)"
    ```

4.  通过托管Prometheus的GPU APP组件来查看容器状态。

    可以看到之前的容器一直处于平稳运行之中，并没有受到新部署应用的影响。

    ![GPU APP资源隔离](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/5875659951/p101900.png)


