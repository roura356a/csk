---
keyword: [MIG多GPU分区技术, A100 GPU MIG功能]
---

# 在Kubernetes集群上使用A100 GPU MIG功能

A100 GPU在性能上相比V100 GPU有较大的提升，并装载了第三代TensorCore。在A100 GPU上实现MIG（Multi-Instance GPU）多GPU分区技术，能在保障QoS和故障隔离的情况下将一块GPU虚分成不同大小的GPU实例。利用MIG功能能够将GPU变成一个灵活的资源池，根据您的需求动态分配不同大小的GPU实例。本文介绍如何在Kubernetes集群上使用A100 GPU MIG功能。

-   已创建一个ACK集群。具体操作，请参见[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。

    **说明：** A100 GPU MIG功能适应于ACK专有版集群、ACK托管版集群和ACK Pro版集群。

-   集群的Kubernetes版本大于等于1.18.8。
-   使用本文功能有可用区和库存的限制，如果在创建新节点池的过程中没有**ecs.ebmgn7.26xlarge**规格，请[提交工单](https://workorder-intl.console.aliyun.com/console.htm)申请。
-   集群有一台启动SSH，且公网可访问的跳板机。具体操作，请参见[如何为已有集群开启SNAT](/intl.zh-CN/Kubernetes集群用户指南/网络/容器网络CNI/为已有集群开启SNAT公网访问能力.md)。

A100 GPU专为科学计算、云图形和数据分析而构建。

**TensorCore**

A100 GPU拥有19.5 Teraflops的FP32计算力、6912个FP32 CUDA Core，且平均每个SM上有64个40 GB内存，在性能上相比V100 GPU有较大的提升，具体有以下几个方面：

-   装载了第三代TensorCore，在HPC和深度学习场景的稀疏矩阵，其吞吐是V100 GPU的2倍。
-   第三代TensorCore加速支持FP16、BF16、TF32、FP64、INT8、INT4和Binary等数据类型。
-   支持更多精度的选择。且TF32计算可以加速深度学习和HPC应用中的FP32的输入和输出，速度是V100 GPU的FP32 FMA的10倍、稀疏矩阵下20倍。
-   FP16和FP32混合精度的深度学习任务，其速度是V100 GPU的2.5倍，稀疏矩阵下5倍。
-   INT8比V100 GPU INT8的性能提升20倍。
-   HPC FP64处理速度比V100 GPU快2.5倍。

**MIG**

您无需花费额外费用，MIG虚拟化技术和GPU分区能力即可为您提供多达7倍的GPU实例。

MIG功能可以将单个GPU实例划分为多个GPU分区，每个GPU实例分区的SMs在整个内存系统中都有独立的路径。每个GPU实例都会被分配唯一的片上交叉条端口、二级缓存库、内存控制器和DRAM地址总线。这样可以确保单个用户的工作负载，即使GPU实例的二级缓存和DRAM负载非常高，也不会对其他分区造成影响，为每个GPU实例分区提供了QoS和故障隔离。

![GPU20](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2791360261/p271760.png)

## 步骤一：创建实例

容器服务ACK提供添加MIG GPU实例的功能，在控制台上可以一键购买或者添加带有A100的GPU实例。

登录[容器服务管理控制台](https://cs.console.aliyun.com)，选择目标集群创建节点池，在**创建节点池**对话框，**实例规格**选择 **ecs.ebmgn7.26xlarge** 。

具体操作，请参见[创建节点池](/intl.zh-CN/Kubernetes集群用户指南/节点与节点池/节点池/创建节点池.md)。

## 步骤二：配置MIG

1.  登录到目标GPU实例，执行以下命令查看GPU信息。

    ```
    nvidia-smi
    ```

    **说明：** 关于如何登录到GPU实例，请参见[通过密码认证登录Linux实例](/intl.zh-CN/实例/连接实例/使用VNC连接实例/通过密码认证登录Linux实例.md)或[通过密码认证登录Windows实例](/intl.zh-CN/实例/连接实例/使用VNC连接实例/通过密码认证登录Windows实例.md)。

    预期输出：

    ![G12](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5227381261/p260643.png)

    从上图输出的信息，可知当前GPU节点上有8个A100 GPU实例，每个A100 GPU实例都可以独立完整运行，且未开启MIG功能。

2.  执行以下命令，查看指定A100 GPU实例支持MIG的规格。

    ```
    nvidia-smi mig -i 0 -lgip
    ```

    预期输出：

    ![g14](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5227381261/p260650.png)

    从上图输出的信息可知：

    -   ID为0的A100 GPU实例，支持5个分区规格，分别是：`1g.5gb`、`2g.10gb`、`3g.20gb`、`4g.20gb`和`7g.40gb` ，它们分别代表不同的显存大小。由于分区规格的大小不同，但每个A100 GPU的显存大小是固定的，所以不同分区规格支持的分区数量也不同。
    -   根据第二列的ID可以知道每个分区规格的标识。
3.  执行以下命令，对ID为0的A100 GPU实例做MIG分区。

    ```
    nvidia-smi mig -i 0 -cgi 9,14,19,19
    ```

    预期输出：

    ```
    Successfully created GPU instance ID  2 on GPU  0 using profile MIG 3g.20gb (ID  9)
    Successfully created GPU instance ID  3 on GPU  0 using profile MIG 2g.10gb (ID 14)
    Successfully created GPU instance ID  9 on GPU  0 using profile MIG 1g.5gb (ID 19)
    Successfully created GPU instance ID 10 on GPU  0 using profile MIG 1g.5gb (ID 19)
    ```

    ```
    nvidia-smi mig -i 0 -cci
    ```

    预期输出：

    ```
    Successfully created compute instance ID  0 on GPU  0 GPU instance ID  9 using profile MIG 1g.5gb (ID  0)
    Successfully created compute instance ID  0 on GPU  0 GPU instance ID 10 using profile MIG 1g.5gb (ID  0)
    Successfully created compute instance ID  0 on GPU  0 GPU instance ID  3 using profile MIG 2g.10gb (ID  1)
    Successfully created compute instance ID  0 on GPU  0 GPU instance ID  2 using profile MIG 3g.20gb (ID  2)
    ```

    从上述输出信息，可知将ID为0的A100 GPU实例虚分为4块GPU实例：一个ID是9（`3g.20gb`）、一个ID是14（`2g.10gb`）和2个ID是19（`1g.5gb`）。

4.  执行以下命令，查看MIG实例分区信息。

    ```
    nvidia-smi
    ```

    在`MIG device`这一栏中可以看到类似以下的MIG实例信息：

    ```
    +-----------------------------------------------------------------------------+
    | MIG devices:                                                                |
    +------------------+----------------------+-----------+-----------------------+
    | GPU  GI  CI  MIG |         Memory-Usage |        Vol|         Shared        |
    |      ID  ID  Dev |           BAR1-Usage | SM     Unc| CE  ENC  DEC  OFA  JPG|
    |                  |                      |        ECC|                       |
    |==================+======================+===========+=======================|
    |  0    2   0   0  |     11MiB / 20096MiB | 42      0 |  3   0    2    0    0 |
    |                  |      0MiB / 32767MiB |           |                       |
    +------------------+----------------------+-----------+-----------------------+
    |  0    3   0   1  |      7MiB /  9984MiB | 28      0 |  2   0    1    0    0 |
    |                  |      0MiB / 16383MiB |           |                       |
    +------------------+----------------------+-----------+-----------------------+
    |  0    9   0   2  |      3MiB /  4864MiB | 14      0 |  1   0    0    0    0 |
    |                  |      0MiB /  8191MiB |           |                       |
    +------------------+----------------------+-----------+-----------------------+
    |  0   10   0   3  |      3MiB /  4864MiB | 14      0 |  1   0    0    0    0 |
    |                  |      0MiB /  8191MiB |           |                       |
    +------------------+----------------------+-----------+-----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    ```

    根据这个方法，可以将GPU节点上所有的A100 GPU根据您的需求进行MIG配置。


## 步骤三：更新Device Plugin配置

1.  当GPU节点上所有A100 GPU做完MIG配置后，执行以下命令，更新GPU实例的Device Plugin，打开MIG策略。

    ```
    sed -i 's/"--pass-device-specs"/"--pass-device-specs", "--mig-strategy=mixed"/g' /etc/kubernetes/manifests/nvidia-device-plugin.yml
    ```

2.  执行以下命令，检测Device Plugin是否运行正常。

    ```
    kubectl -n kube-system get po | grep nvidia-device
    ```

    预期输出：

    ```
    nvidia-device-plugin-cn-hangzhou.xxxxxx   1/1     Running   0          36m
    ```

3.  DevicePlugin正常运行后，执行以下命令，查看GPU实例上配置的MIG实例和对应的资源。

    ```
    kubectl describe node <your node>
    ```

    预期输出：

    ```
    Capacity:
     cpu:                     104
     ephemeral-storage:       123722704Ki
     hugepages-1Gi:           0
     hugepages-2Mi:           0
     memory:                  791733364Ki
     nvidia.com/gpu:          0
     nvidia.com/mig-1g.5gb:   16
     nvidia.com/mig-2g.10gb:  8
     nvidia.com/mig-3g.20gb:  8
    ```


## 步骤四：部署应用

使用以下YAML文件样例，部署应用，声明使用型号为**mig-2g.10gb** 的GPU实例。

```
kubectl apply -f - <<EOF
apiVersion: v1
kind: Pod
metadata:
  name: smi
spec:
  restartPolicy: OnFailure
  containers:
  - name: nvidia-smi
    command:
    - nvidia-smi 
    - -L
    image: nvidia/cuda:9.0-base
    resources:
      limits:
        nvidia.com/mig-2g.10gb: 1
      requests:
        nvidia.com/mig-2g.10gb: 1
EOF
```

执行以下命令，查看日志信息。

```
kubectl logs smi
```

预期输出：

```
GPU 0: A100-SXM4-40GB (UUID: GPU-7780f282-99a1-7024-f7a4-65a55230****)
  MIG 2g.10gb Device 0: (UUID: MIG-GPU-7780f282-99a1-7024-f7a4-65a55230****/3/0)
```

