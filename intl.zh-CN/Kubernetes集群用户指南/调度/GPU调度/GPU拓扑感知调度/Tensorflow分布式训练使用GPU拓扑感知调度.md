---
keyword: [GPU拓扑感知调度, Kubernetes调度, 深度学习, Tensorflow分布式训练]
---

# Tensorflow分布式训练使用GPU拓扑感知调度

ACK基于Scheduling Framework机制，实现GPU拓扑感知调度，即在节点的GPU组合中选择具有最优训练速度的组合。本文主要介绍如何使用GPU拓扑感知调度来提升Tensorflow分布式训练的训练速度。

-   [创建ACK Pro版集群](/intl.zh-CN/Kubernetes集群用户指南/ACK Pro集群/创建ACK Pro版集群.md)。
-   安装[Arena](https://github.com/kubeflow/arena)。
-   [安装ack-ai-installer组件](/intl.zh-CN/Kubernetes集群用户指南/调度/组件安装及功能简介/ack-ai-installer/安装ack-ai-installer组件.md)。
-   系统组件版本要求具体如下表所示。

    |组件|版本要求|
    |--|----|
    |Kubernetes|1.18.8及以上版本|
    |Helm|3.0及以上版本|
    |Nvidia|418.87.01及以上版本|
    |训练框架NCCL版本|2.7+|
    |Docker|19.03.5|
    |操作系统|CentOS 7.6、CentOS 7.7、Ubuntu 16.04和Ubuntu 18.04、Alibaba Cloud Linux 2|
    |显卡|V100|


## 应用限制

-   仅支持MPI作业的分布式训练。
-   只有当提交作业的所有Pod对资源请求都满足条件时，才能创建Pod并启动作业，否则请求会处于资源等待状态。

## 操作步骤

**节点配置**

执行以下命令设置节点Label，显式激活节点GPU拓扑感知调度。

```
kubectl label node <Your Node Name\> ack.node.gpu.schedule=topology
```

**说明：** 当节点激活GPU拓扑感知调度后，不再支持普通GPU资源调度。可执行以下命令更改Label，恢复普通GPU资源调度功能。

```
kubectl label node <Your Node Name\> ack.node.gpu.schedule=default --overwrite
```

**提交作业**

提交MPI作业，并设置--gputopology为`true`。

```
arena submit mpi --gputopology=true ***
```

## 示例一：训练Vgg16

**说明：** 本文测试集群有2台8卡V100机器。

**使用GPU拓扑感知调度训练Vgg16**

1.  使用以下命令向集群提交作业。

    ```
    arena submit mpi \
      --name=tensorflow-topo-4-vgg16 \
      --gpus=1 \
      --workers=4 \
      --gputopology=true \
      --image=registry.cn-hangzhou.aliyuncs.com/kubernetes-image-hub/tensorflow-benchmark:tf2.3.0-py3.7-cuda10.1 \
      "mpirun --allow-run-as-root -np "4" -bind-to none -map-by slot -x NCCL_DEBUG=INFO -x NCCL_SOCKET_IFNAME=eth0 -x LD_LIBRARY_PATH -x PATH --mca pml ob1 --mca btl_tcp_if_include eth0 --mca oob_tcp_if_include eth0 --mca orte_keep_fqdn_hostnames t --mca btl ^openib python /tensorflow/benchmarks/scripts/tf_cnn_benchmarks/tf_cnn_benchmarks.py --model=vgg16 --batch_size=64 --variable_update=horovod"
    ```

2.  执行以下命令，查看当前作业运行情况。

    ```
    arena get tensorflow-topo-4-vgg16 --type mpijob
    ```

    预期输出：

    ```
    Name:      tensorflow-topo-4-vgg16
    Status:    RUNNINGNamespace: default
    Priority:  N/A
    Trainer:   MPIJOB
    Duration:  2m
    
    Instances:
      NAME                                    STATUS   AGE  IS_CHIEF  GPU(Requested)  NODE
      ----                                    ------   ---  --------  --------------  ----
      tensorflow-topo-4-vgg16-launcher-lmhjl  Running  2m   true      0               cn-shanghai.192.168.16.172
      tensorflow-topo-4-vgg16-worker-0        Running  2m   false     1               cn-shanghai.192.168.16.173
      tensorflow-topo-4-vgg16-worker-1        Running  2m   false     1               cn-shanghai.192.168.16.173
      tensorflow-topo-4-vgg16-worker-2        Running  2m   false     1               cn-shanghai.192.168.16.173
      tensorflow-topo-4-vgg16-worker-3        Running  2m   false     1               cn-shanghai.192.168.16.173
    ```

3.  执行以下命令，查看当前日志信息。

    ```
    arena logs -f tensorflow-topo-4-vgg16
    ```

    预期输出：

    ```
    total images/sec: 991.92
    ```


**使用普通GPU调度训练Vgg16**

1.  使用以下命令向集群提交作业。

    ```
    arena submit mpi \
      --name=tensorflow-4-vgg16 \
      --gpus=1 \
      --workers=4 \
      --image=registry.cn-hangzhou.aliyuncs.com/kubernetes-image-hub/tensorflow-benchmark:tf2.3.0-py3.7-cuda10.1 \
      "mpirun --allow-run-as-root -np "4" -bind-to none -map-by slot -x NCCL_DEBUG=INFO -x NCCL_SOCKET_IFNAME=eth0 -x LD_LIBRARY_PATH -x PATH --mca pml ob1 --mca btl_tcp_if_include eth0 --mca oob_tcp_if_include eth0 --mca orte_keep_fqdn_hostnames t --mca btl ^openib python /tensorflow/benchmarks/scripts/tf_cnn_benchmarks/tf_cnn_benchmarks.py --model=vgg16 --batch_size=64 --variable_update=horovod"
    ```

2.  执行以下命令，查看当前作业运行情况。

    ```
    arena get tensorflow-4-vgg16 --type mpijob
    ```

    预期输出：

    ```
    Name:      tensorflow-4-vgg16
    Status:    RUNNING
    Namespace: default
    Priority:  N/A
    Trainer:   MPIJOB
    Duration:  9s
    
    Instances:
      NAME                               STATUS   AGE  IS_CHIEF  GPU(Requested)  NODE
      ----                               ------   ---  --------  --------------  ----
      tensorflow-4-vgg16-launcher-xc28k  Running  9s   true      0               cn-shanghai.192.168.16.172
      tensorflow-4-vgg16-worker-0        Running  9s   false     1               cn-shanghai.192.168.16.172
      tensorflow-4-vgg16-worker-1        Running  9s   false     1               cn-shanghai.192.168.16.173
      tensorflow-4-vgg16-worker-2        Running  9s   false     1               cn-shanghai.192.168.16.172
      tensorflow-4-vgg16-worker-3        Running  9s   false     1               cn-shanghai.192.168.16.173
    ```

3.  执行以下命令，查看当前日志信息。

    ```
    arena logs -f tensorflow-4-vgg16
    ```

    预期输出：

    ```
    total images/sec: 200.47
    ```


## 示例二：训练Resnet50

**使用GPU拓扑感知调度训练Resnet50**

1.  使用以下命令向集群提交作业。

    ```
    arena submit mpi \
      --name=tensorflow-topo-4-resnet50 \
      --gpus=1 \
      --workers=4 \
      --gputopology=true \
      --image=registry.cn-hangzhou.aliyuncs.com/kubernetes-image-hub/tensorflow-benchmark:tf2.3.0-py3.7-cuda10.1 \
      "mpirun --allow-run-as-root -np "4" -bind-to none -map-by slot -x NCCL_DEBUG=INFO -x NCCL_SOCKET_IFNAME=eth0 -x LD_LIBRARY_PATH -x PATH --mca pml ob1 --mca btl_tcp_if_include eth0 --mca oob_tcp_if_include eth0 --mca orte_keep_fqdn_hostnames t --mca btl ^openib python /tensorflow/benchmarks/scripts/tf_cnn_benchmarks/tf_cnn_benchmarks.py --model=resnet50 --batch_size=64  --variable_update=horovod"
    ```

2.  执行以下命令，查看当前作业运行情况。

    ```
    arena get tensorflow-topo-4-resnet50 --type mpijob
    ```

    预期输出：

    ```
    Name:      tensorflow-topo-4-resnet50
    Status:    RUNNING
    Namespace: default
    Priority:  N/A
    Trainer:   MPIJOB
    Duration:  8s
    
    Instances:
      NAME                                       STATUS   AGE  IS_CHIEF  GPU(Requested)  NODE
      ----                                       ------   ---  --------  --------------  ----
      tensorflow-topo-4-resnet50-launcher-7ln8j  Running  8s   true      0               cn-shanghai.192.168.16.172
      tensorflow-topo-4-resnet50-worker-0        Running  8s   false     1               cn-shanghai.192.168.16.173
      tensorflow-topo-4-resnet50-worker-1        Running  8s   false     1               cn-shanghai.192.168.16.173
      tensorflow-topo-4-resnet50-worker-2        Running  8s   false     1               cn-shanghai.192.168.16.173
      tensorflow-topo-4-resnet50-worker-3        Running  8s   false     1               cn-shanghai.192.168.16.173
    ```

3.  执行以下命令，查看当前日志信息。

    ```
    arena logs -f tensorflow-topo-4-resnet50
    ```

    预期输出：

    ```
    total images/sec: 1471.55
    ```


**使用普通GPU调度训练Resnet50**

1.  使用以下命令向集群提交作业。

    ```
    arena submit mpi \
      --name=tensorflow-4-resnet50 \
      --gpus=1 \
      --workers=4 \
      --image=registry.cn-hangzhou.aliyuncs.com/kubernetes-image-hub/tensorflow-benchmark:tf2.3.0-py3.7-cuda10.1 \
      "mpirun --allow-run-as-root -np "4" -bind-to none -map-by slot -x NCCL_DEBUG=INFO -x NCCL_SOCKET_IFNAME=eth0 -x LD_LIBRARY_PATH -x PATH --mca pml ob1 --mca btl_tcp_if_include eth0 --mca oob_tcp_if_include eth0 --mca orte_keep_fqdn_hostnames t --mca btl ^openib python /tensorflow/benchmarks/scripts/tf_cnn_benchmarks/tf_cnn_benchmarks.py --model=resnet50 --batch_size=64  --variable_update=horovod"
    ```

2.  执行以下命令，查看当前作业运行情况。

    ```
    arena get tensorflow-4-resnet50 --type mpijob
    ```

    预期输出：

    ```
    Name:      tensorflow-4-resnet50
    Status:    RUNNING
    Namespace: default
    Priority:  N/A
    Trainer:   MPIJOB
    Duration:  9s
    
    Instances:
      NAME                                  STATUS   AGE  IS_CHIEF  GPU(Requested)  NODE
      ----                                  ------   ---  --------  --------------  ----
      tensorflow-4-resnet50-launcher-q24hv  Running  9s   true      0               cn-shanghai.192.168.16.172
      tensorflow-4-resnet50-worker-0        Running  9s   false     1               cn-shanghai.192.168.16.172
      tensorflow-4-resnet50-worker-1        Running  9s   false     1               cn-shanghai.192.168.16.173
      tensorflow-4-resnet50-worker-2        Running  9s   false     1               cn-shanghai.192.168.16.172
      tensorflow-4-resnet50-worker-3        Running  9s   false     1               cn-shanghai.192.168.16.173
    ```

3.  执行以下命令，查看当前日志信息。

    ```
    arena logs -f tensorflow-4-resnet50
    ```

    预期输出：

    ```
    total images/sec: 745.38
    ```


## 性能对比

基于以上4个测试用例性能对比结果如下：

![GPU31](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6590658161/p264372.png)

基于以上4个测试用例的性能对比图，可知经过GPU拓扑感知调度后，Tensorflow分布式训练的效果有了很大的提升。

**说明：** GPU拓扑感知调度提升结果与您使用的模型以及集群的环境有一定关系，您可以参考上述使用示例，评测自己的模型。

[背景概述](/intl.zh-CN/Kubernetes集群用户指南/调度/GPU调度/GPU拓扑感知调度/GPU拓扑感知调度背景概述.md)

[安装ack-ai-installer组件](/intl.zh-CN/Kubernetes集群用户指南/调度/组件安装及功能简介/ack-ai-installer/安装ack-ai-installer组件.md)

