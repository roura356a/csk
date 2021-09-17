---
keyword: [共享调度, 多卡共享策略, 共享GPU]
---

# 通过共享GPU实现多卡共享策略

ACK Pro版集群支持共享GPU，共享GPU能够在Kubernetes上实现GPU共享调度和显存隔离。本文介绍如何设置共享GPU的多卡共享策略。

已安装共享GPU组件和资源工具。具体操作，请参见[安装并使用共享GPU组件和资源工具](/intl.zh-CN/Kubernetes集群用户指南/GPU/NPU/共享GPU调度/专业版/安装并使用共享GPU组件和资源工具.md)。

多卡共享策略指的是某个应用申请了N个GiB的显存，并指定了这N个GiB的显存由M块GPU卡分配，每块GPU卡分配的显存为N/M（目前N/M必须为整数，并且这M张GPU卡必须在同一个Kubernetes节点上）。例如，某个应用申请了8 GiB显存，并指定了GPU卡个数为4，那么某个节点需分配4块GPU卡给该应用，每块GPU卡分配2 GiB显存。

## 操作步骤

1.  创建binpack-1.yaml。

    使用YAML文件创建一个资源类型为StatefulSet的binpack-1应用，并在YAML文件中设置以下配置。

    -   设置Replicas为1。
    -   申明每个Pod使用4张GPU卡，每张GPU卡使用2 GiB显存。
        -   给Pod添加标签`aliyun.com/gpu-count=4`。

            **说明：** 将标签添加在`spec.template.metadata.labels`下。

        -   指定Pod的扩展资源`aliyun.com/gpu-mem`数量为8。
    ```
    apiVersion: apps/v1
    kind: StatefulSet
    metadata:
      name: binpack-1
      labels:
        app: binpack-1
    spec:
      # 副本数设置1，便于观察。
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
            # 申明8 GiB显存由4块GPU卡提供，每块提供2 GiB显存。
            aliyun.com/gpu-count: "4"
        spec:
          containers:
          - name: binpack-1
            image: registry.cn-shanghai.aliyuncs.com/tensorflow-samples/tensorflow-gpu-mem:10.0-runtime-centos7
            resources:
              limits:
                # 单位为GiB，该Pod总共申请了8 GiB显存。
                aliyun.com/gpu-mem: 8
    ```

2.  部署binpack-1应用。

    ```
    kubectl apply -f binpack-1.yaml
    ```


## 验证多卡共享策略

1.  查看Pod运行状态。

    ```
    kubectl get pod
    ```

    预期输出：

    ```
    NAME               READY   STATUS      RESTARTS   AGE
    binpack-1-0        1/1     Running     0          9m34s
    ```

2.  查看GPU数量和GPU的显存。

    ```
    kubectl exec binpack-1-0 -- nvidia-smi
    ```

    预期输出：

    ```
    Tue Nov  3 09:05:05 2020
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 418.87.01    Driver Version: 418.87.01    CUDA Version: 10.1     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |===============================+======================+======================|
    |   0  Tesla V100-SXM2...  On   | 00000000:00:07.0 Off |                    0 |
    | N/A   31C    P0    54W / 300W |   2084MiB /  2150MiB |      1%      Default |
    +-------------------------------+----------------------+----------------------+
    |   1  Tesla V100-SXM2...  On   | 00000000:00:08.0 Off |                    0 |
    | N/A   32C    P0    55W / 300W |   2084MiB /  2150MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    |   2  Tesla V100-SXM2...  On   | 00000000:00:09.0 Off |                    0 |
    | N/A   32C    P0    54W / 300W |   2084MiB /  2150MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    |   3  Tesla V100-SXM2...  On   | 00000000:00:0A.0 Off |                    0 |
    | N/A   34C    P0    54W / 300W |   2084MiB /  2150MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                       GPU Memory |
    |  GPU       PID   Type   Process name                             Usage      |
    |=============================================================================|
    +-----------------------------------------------------------------------------+
    ```

    可以看到使用了4张GPU卡，且每张GPU卡的显存大小与所申请的显卡大小基本一致，GPU卡的显存大小为2 GiB。

    **说明：** 每张GPU卡的可使用显存大小与所申请的显卡大小有可能存在几十到几百兆的误差。

3.  查看容器使用的GPU卡。

    ```
    kubectl exec binpack-1-0 -- env | grep NVIDIA_VISIBLE_DEVICES
    ```

    预期输出：

    ```
    NVIDIA_VISIBLE_DEVICES=GPU-c09d11f5-a565-1f83-903c-a65a7ba138af,GPU-959873c5-b57b-5d24-f3aa-e700c5d431cb,GPU-abdcd4da-7505-c267-fb4d-30478a360e07,GPU-ba997778-f8ce-e433-3c84-6d15392a06a1
    ```

4.  通过环境变量`ALIYUN_COM_GPU_MEM_CONTAINER`查看容器在每张GPU卡上可使用的显存大小。

    ```
    kubectl exec binpack-1-0 -- env | grep ALIYUN
    ```

    预期输出：

    ```
    ALIYUN_COM_GPU_MEM_CONTAINER=2 # 该值表示每张卡上该容器可使用的显存为2 GiB，容器内的应用程序可以读取该值设置显存限制。
    ALIYUN_COM_GPU_MEM_DEV=15 # 每张卡的总显存为15 GiB。
    ```


