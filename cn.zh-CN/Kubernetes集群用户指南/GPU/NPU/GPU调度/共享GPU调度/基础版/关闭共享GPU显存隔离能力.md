---
keyword: [GPU, 共享GPU显存隔离]
---

# 关闭共享GPU显存隔离能力

本文通过部署一个简单的示例应用向您介绍如何在集群中关闭共享GPU显存隔离能力。

## 适用场景

本文示例适用于已开启共享GPU显存隔离能力的专有版GPU集群及ACK Pro版集群。

## 前提条件

已安装共享GPU组件。具体操作，请参见[安装共享GPU组件](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/GPU调度/共享GPU调度/基础版/安装共享GPU组件.md)或[安装并使用共享GPU组件和资源工具](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/GPU调度/共享GPU调度/专业版/安装并使用共享GPU组件和资源工具.md)。

## 操作步骤

1.  执行以下命令查询集群的GPU共享能力。

    ```
    kubectl inspect cgpu
    ```

    预期输出：

    ```
    NAME                      IPADDRESS      GPU0(Allocated/Total)  GPU Memory(GiB)
    cn-beijing.192.16x.x.xx3  192.16x.x.xx3  0/15                   0/15
    cn-beijing.192.16x.x.xx1  192.16x.x.xx1  0/15                   0/15
    cn-beijing.192.16x.x.xx2  192.16x.x.xx2  0/15                   0/15
    ---------------------------------------------------------------------
    Allocated/Total GPU Memory In Cluster:
    0/45 (0%)
    ```

    **说明：** 如果您想查询GPU共享能力详细信息，请执行命令`kubectl inspect cgpu -d`。

2.  使用以下模板创建GPU共享容器，同时该容器不使用共享GPU的隔离能力。

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
            env:
              - name: CGPU_DISABLE #关闭cGPU隔离能力。
                value: "true"
            resources:
              limits:
                # GiB
                aliyun.com/gpu-mem: 3
    ```

    **说明：**

    -   aliyun.com/gpu-mem：设置GPU显存大小。
    -   关闭共享GPU显存隔离能力：设置容器的环境变量CGPU\_DISABLE为true即可关闭cGPU。
3.  执行以下命令，查看共享GPU调度结果。

    ```
    kubectl inspect cgpu
    ```

    预期输出：

    ```
    NAME                      IPADDRESS      GPU0(Allocated/Total)  GPU Memory(GiB)
    cn-beijing.192.16x.x.xx1  192.16x.x.xx1  0/15                   0/15
    cn-beijing.192.16x.x.xx2  192.16x.x.xx2  0/15                   0/15
    cn-beijing.192.16x.x.xx3  192.16x.x.xx3  3/15                   3/15
    ---------------------------------------------------------------------
    Allocated/Total GPU Memory In Cluster:
    3/45 (6%)
    ```

    节点cn-beijing.192.16x.x.xx3分配了3 GiB显存给刚刚创建的容器。


## 执行结果

您可以通过以下两种方式验证部署的共享GPU显存隔离能力是否被关闭：

-   方式一：执行以下命令查看部署应用的日志。

    ```
    kubectl logs binpack-0 --tail=1
    ```

    预期输出：

    ```
    2020-08-25 08:14:54.927965: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1326] Created TensorFlow device (/job:localhost/replica:0/task:0/device:GPU:0 with 15024 MB memory) -> physical GPU (device: 0, name: Tesla V100-SXM2-16GB, pci bus id: 0000:00:07.0, compute capability: 7.0)
    ```

    从日志中可以看到，容器中的应用程序能够使用的显存为15024 MiB，可证明共享GPU显存隔离能力被关闭了（共享GPU显存隔离能力开启时，应用程序能够看到的显存为3 GiB）。

-   方式二：执行以下命令登录容器查看容器被分配显存总量。

    ```
    kubectl exec binpack-0 nvidia-smi
    ```

    预期输出：

    ```
    Tue Aug 25 08:23:33 2020
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 418.87.01    Driver Version: 418.87.01    CUDA Version: 10.1     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |===============================+======================+======================|
    |   0  Tesla V100-SXM2...  Off  | 00000000:00:07.0 Off |                    0 |
    | N/A   33C    P0    55W / 300W |  15453MiB / 16130MiB |      1%      Default |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                       GPU Memory |
    |  GPU       PID   Type   Process name                             Usage      |
    |=============================================================================|
    +-----------------------------------------------------------------------------+
    ```

    从输出信息中可以看到宿主机的显存容量为16130 MiB，容器分配到的显存为15453 MiB，可证明共享GPU隔离能力未生效（共享GPU隔离能力开启时，容器分配到的显存为3 GiB）。


**相关文档**  


[共享GPU调度概述](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/GPU调度/共享GPU调度/共享GPU调度概述.md)

[安装共享GPU组件](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/GPU调度/共享GPU调度/基础版/安装共享GPU组件.md)

[监控和隔离GPU资源](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/GPU调度/共享GPU调度/基础版/监控和隔离GPU资源.md)

