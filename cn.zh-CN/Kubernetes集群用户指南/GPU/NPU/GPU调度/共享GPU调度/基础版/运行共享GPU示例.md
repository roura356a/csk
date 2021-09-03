---
keyword: [共享GPU, 运行共享GPU]
---

# 运行共享GPU示例

在安装完共享GPU组件后，本文将介绍如何在专有版GPU集群中使用共享GPU能力。

已安装共享GPU组件。具体操作，请参见[安装共享GPU组件](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/GPU调度/共享GPU调度/基础版/安装共享GPU组件.md)。

## 适用场景

-   本文所描述的组件，仅支持专有版GPU集群，不支持托管版GPU集群。
-   如果您想在ACK Pro版集群运行共享GPU示例，请参见[运行共享GPU示例](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/GPU调度/共享GPU调度/专业版/运行共享GPU示例.md)。

## 操作步骤

1.  登录到Master节点，执行以下命令查询集群的GPU共享能力。

    **说明：**

    -   关于如何登录到Master节点，请参见[通过密码认证登录Linux实例](/cn.zh-CN/实例/连接实例/使用VNC连接实例/通过密码认证登录Linux实例.md)或[通过密码认证登录Windows实例](/cn.zh-CN/实例/连接实例/使用VNC连接实例/通过密码认证登录Windows实例.md)。
    -   如果您需要在本地客户端执行命令查询集群的GPU共享能力，请安装共享GPU组件和资源工具。具体操作，请参见[步骤四：安装和使用GPU资源查询工具](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/GPU调度/共享GPU调度/专业版/安装并使用共享GPU组件和资源工具.md)。
    ```
    kubectl inspect cgpu
    ```

    预期输出：

    ```
    NAME                     IPADDRESS    GPU0(Allocated/Total)  GPU1(Allocated/Total)  GPU Memory(GiB)
    cn-shanghai.192.168.0.4  192.168.0.4  0/7                    0/7                    0/14
    ---------------------------------------------------------------------
    Allocated/Total GPU Memory In Cluster:
    0/14 (0%)
    ```

    **说明：** 如果想查询GPU共享能力详细信息，执行命令kubectl inspect cgpu -d。

2.  部署共享GPU示例应用，该应用申请3 GiB显存。

    ```
    apiVersion: apps/v1
    kind: StatefulSet
    
    metadata:
      name: binpack
      labels:
        app: binpack
    
    spec:
      replicas: 1
      serviceName: "binpack-1"
      podManagementPolicy: "Parallel"
      selector: # define how the deployment finds the pods it manages
        matchLabels:
          app: binpack-1
    
      template: # define the pods specifications
        metadata:
          labels:
            app: binpack-1
    
        spec:
          containers:
          - name: binpack-1
            image: registry.cn-shanghai.aliyuncs.com/tensorflow-samples/tensorflow-gpu-mem:10.0-runtime-centos7
            command:
              - python3
              - /app/main.py
            resources:
              limits:
                # GiB
                aliyun.com/gpu-mem: 3
    ```

    **说明：** aliyun.com/gpu-mem：设置GPU显存大小。

3.  执行以下命令，查看集群GPU显存使用情况。

    ```
    kubectl inspect cgpu
    ```

    预期输出：

    ```
    NAME                      IPADDRESS      GPU0(Allocated/Total)  GPU Memory(GiB)
    cn-beijing.192.168.1.105  192.168.1.105  3/14                   3/14
    ---------------------------------------------------------------------
    Allocated/Total GPU Memory In Cluster:
    3/14 (21%)
    ```

    可以看到节点cn-beijing.192.168.1.105总共有14 GiB显存，已分配3 GiB显存。


您可以通过以下方式验证节点GPU显存隔离能力是否生效。

-   执行以下命令查看步骤2中部署应用的日志。

    通过查看日志，您可以确定cGPU显存隔离是否成功部署。

    ```
    kubectl logs binpack-0 --tail=1
    ```

    预期输出：

    ```
    2020-03-13 09:14:13.931003: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1326] Created TensorFlow device (/job:localhost/replica:0/task:0/device:GPU:0 with 2832 MB memory) -> physical GPU (device: 0, name: Tesla T4, pci bus id: 0000:00:07.0, compute capability: 7.5)
    ```

    可以看到容器申请的显存为2832 MiB。

-   执行以下命令登录容器查看容器被分配显存总量。

    ```
    kubectl exec -it binpack-0 nvidia-smi
    ```

    预期输出：

    ```
    Fri Mar 13 09:32:18 2020
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 418.87.01    Driver Version: 418.87.01    CUDA Version: 10.1     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00000000:00:07.0 Off |                    0 |
    | N/A   41C    P0    26W /  70W |   3043MiB /  3231MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                       GPU Memory |
    |  GPU       PID   Type   Process name                             Usage      |
    |=============================================================================|
    +-----------------------------------------------------------------------------+
    ```

    可以看到该容器被分配显存总量为3231 MiB 。

-   执行以下命令查看示例应用所在节点的GPU显存总量（在节点上执行）。

    ```
    nvidia-smi
    ```

    预期输出：

    ```
    Fri Mar 13 17:36:24 2020
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 418.87.01    Driver Version: 418.87.01    CUDA Version: 10.1     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00000000:00:07.0 Off |                    0 |
    | N/A   40C    P0    26W /  70W |   3053MiB / 15079MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                       GPU Memory |
    |  GPU       PID   Type   Process name                             Usage      |
    |=============================================================================|
    |    0      8796      C   python3                                     3043MiB |
    +-----------------------------------------------------------------------------+
    
                            
    ```

    可以看到主机上的显存总量为15079 MiB，其中分配给容器的是3053 MiB。


**相关文档**  


[共享GPU调度概述](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/GPU调度/共享GPU调度/共享GPU调度概述.md)

[安装共享GPU组件](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/GPU调度/共享GPU调度/基础版/安装共享GPU组件.md)

[监控和隔离GPU资源](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/GPU调度/共享GPU调度/基础版/监控和隔离GPU资源.md)

