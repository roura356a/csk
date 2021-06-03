---
keyword: [云原生AI, 监控, GPU资源]
---

# 使用云原生AI监控大盘

云原生AI的监控组件能够帮助您从不同的维度（比如：集群、节点、训练任务等）监控集群的GPU资源使用情况，以及集群的各命名空间下的资源配额使用情况。本文分别从集群、节点、训练任务和资源配额维度介绍监控大盘，以及介绍如何安装和使用云原生AI监控大盘。

-   本文仅支持ACK Pro版集群，且Kubernetes集群版本大于等于1.18.8。
-   Arena组件版本大于等于0.7.0。具体操作，请参见：[安装Arena](/cn.zh-CN/解决方案/AI解决方案/环境准备/通过组件安装最新版的Arena.md)。
-   安装ARMS Prometheus监控组件，具体操作，请参见[arms-prometheus](https://cs.console.aliyun.com/#/k8s/catalog/detail/incubator_ack-arms-prometheus)。
-   安装AI运维控制台组件。具体操作，请参见[安装云原生AI套件](/cn.zh-CN/云原生AI用户指南/环境准备/安装云原生AI套件.md)。

当使用Arena提交训练任务后，您可能需要从集群、节点、训练任务或资源配额的维度去查看GPU的使用率、GPU显存的使用情况。传统的ACK集群只能从节点的维度监控该节点的GPU使用情况（使用率、显存使用情况、功率等）或者从Pod维度监控该Pod使用GPU的情况。

基于以上存在的问题，阿里云容器服务研发了云原生AI监控大盘，该大盘相比传统的ACK集群GPU监控大盘，有如下特点：

-   整个监控大盘由四个部分组成：集群、节点、训练任务、资源配额。
-   集群监控大盘主要展示集群总的GPU节点数、不健康GPU节点数、GPU平均利用率、各个状态的训练任务数等。
-   节点监控大盘主要展示每个节点的总GPU卡数、已分配GPU卡数、GPU的使用率、节点GPU显存使用情况等。
-   训练任务监控大盘主要展示每个训练任务的状态、已运行时长、申请GPU数、GPU的平均利用率、GPU显存使用情况等。
-   资源配额监控大盘主要展示每种资源配额在某个Namespace下的分配和使用情况，比如：Max Quota、Min Quota和Used Quota等。

## 安装云原生AI监控组件

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在左侧导航栏，选择**应用** \> **AI工程加速（公测）**。

5.  安装监控组件。

    -   如果您之前已经部署过云原生AI套件：

        在云原生AI套件页面，找到云原生AI监控组件**ack-arena-exporter**，然后单击右侧**操作**列下的**部署**。

    -   如果您之前未部署过云原生AI套件：

        在云原生AI套件页面，单击**一键部署**，然后在**监控**区域，选中**监控组件**后，单击**部署云原生AI套件**。


## 监控大盘介绍

**集群监控大盘**

进入云原生AI运维控制台后，默认进入的是集群监控大盘页面。具体操作，请参见[方式一：使用公网Ingress访问AI运维控制台](/cn.zh-CN/云原生AI用户指南/AI运维控制台/访问AI运维控制台.md)。

集群监控大盘有以下可供您查看的指标：

-   **GPU Summary Of Cluster**：展示集群中总的GPU节点数、已分配的GPU节点数、不健康的GPU节点数。
-   **Total GPU Nodes**：集群中总的GPU节点数。
-   **Unhealthy GPU Nodes**：不健康的GPU节点数。
-   **GPU Memory\(Used/Total\)**：集群已使用GPU显存与总的GPU显存的百分比。
-   **GPU Memory\(Allocated/Total\)**：集群已分配GPU显存与总的GPU显存百分比。
-   **GPU Utilization**：集群GPU的平均利用率。
-   **GPUs\(Allocated/Total\)**：集群已分配GPU卡的个数与总的GPU卡数的百分比。
-   **Training Job Summary Of Cluster**：集群中各种状态（Running、Pending、Succeeded、Failed）的训练任务数。

![jk1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3627862261/p278620.png)

**节点监控大盘**

在集群监控大盘页面，单击右上角的**Nodes**，进入节点监控大盘。

节点监控大盘有以下可供您查看的指标：

-   **GPU Node Details**：以表格的形式展示集群节点的相关信息，包括：
    -   节点名称（Name）
    -   节点在集群中的IP（IP）
    -   节点在集群中的角色（Role）
    -   节点的状态（Status）
    -   GPU模式：独占或共享（GPU Mode）
    -   节点拥有GPU卡的个数（Total GPUs）
    -   节点拥有总的GPU显存（Total GPU Memory）
    -   节点已分配GPU卡数（Allocated GPUs）
    -   节点已分配GPU显存（Allocated GPU Memory）
    -   节点已使用GPU显存（Used GPU Memory）
    -   节点GPU平均使用率（GPU Utilization）
-   **GPU Duty Cycle**：每个节点的每个GPU的使用率。
-   **GPU Memory Usage**：每个节点的每个GPU的显存使用量。
-   **GPU Memory Usage Percentage**：每个节点的每个GPU的显存使用百分比。
-   **Allocated GPUs Per Node**：每个节点已分配的GPU卡数。
-   **GPU Number Per Node**：每个节点的总GPU卡数。
-   **Total GPU Memory Per Node**：每个节点的总GPU显存。

![jk21](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5296342261/p278625.png)

![jk22](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5296342261/p278626.png)

您可以根据节点监控大盘左上角的**gpu\_node**和**GPU**筛选项，对节点和GPU卡进行筛选，以便您查看到目标节点上的GPU使用情况。

![jk23](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4627862261/p278628.png)

**训练任务监控大盘**

在节点监控大盘页面，单击右上角**TrainingJobs**，进入训练任务的监控大盘。

训练任务监控大盘有以下可供您查看的指标：

-   **Training Jobs**：通过表格的形式展示各个训练任务的情况，包括：
    -   训练任务所在命名空间（Namespace）
    -   训练任务名称（Job Name）
    -   训练任务类型（Job Type）
    -   训练任务状态（Job Status）
    -   训练任务持续时间（Duration）
    -   训练任务请求GPU卡数（Request GPUs）
    -   训练任务请求的GPU显存（Allocated GPU Memory）
    -   训练任务当前使用的GPU显存（Used GPU Memory）
    -   训练任务的GPU平均利用率（GPU Utilization）
-   **Job Instance Used GPU Memory**：训练任务中的各个实例的已使用GPU显存。
-   **Job Instance Used GPU Memory Percentage**：训练任务中各个实例使用GPU显存的百分比。
-   **Job Instance GPU Duty Cycle**：训练任务中各个实例的GPU利用率。

![jk31](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5296342261/p278634.png)

您可以根据训练任务监控大盘左上角的**job\_namespace**、**job\_type**和**job\_name**等筛选项，对训练任务从不同维度进行筛选，以便您查看到目标训练任务的具体情况。

![jk32](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4627862261/p278635.png)

**资源配额监控大盘**

在训练任务监控大盘页面，单击右上角的**Quota**，进入资源配额监控大盘。

资源配额监控大盘有以下可供您查看的指标：

-   **Quota\(cpu\)**
-   **Quota\(memory\)**
-   **Quota\(nvidia.com/gpu\)**
-   **Quota\(aliyun.com/gpu-mem\)**
-   **Quota\(aliyun.com/gpu\)**

以上枚举的每一个指标都以表格的形式展示资源配额的相关信息，包括：

-   **Elastic Quota Name**：资源配额的名称。
-   **Namespace**：资源所属的Namespace。
-   **Resource Name**：资源类型的名称。
-   **Max Quota**：您在某个Namespace下某种资源所使用的上限。
-   **Min Quota**：当整个集群资源紧张时，您在某个Namespace下可以使用的保障资源。
-   **Used Quota**: 您在某个Namespace下，某种资源的已使用值。

![jk41](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4627862261/p279120.png)

## 使用云原生AI监控大盘示例

本文以使用Arena提交一个`mpijob`任务，然后查看监控大盘的变化为例，说明如何使用云原生AI监控大盘。

1.  进入云原生AI运维控制台，默认进入的是集群监控大盘页面。

    当集群没有任何训练任务时，集群监控大盘如下。

    ![jk51](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4627862261/p279125.png)

    由上图可知，集群总共有两个GPU节点，总共有2张GPU卡。

2.  执行以下命令，提交一个`mpijob`。

    ```
    arena submit mpijob \
        --name=mpi-test \
        --gpus=1 \
        --workers=1 \
        --working-dir=/perseus-demo/tensorflow-demo/ \
        --image=registry.cn-beijing.aliyuncs.com/ai-samples/horovod:0.13.11-tf1.10.0-torch0.4.0-py3.5 \
        'mpirun python /benchmarks/scripts/tf_cnn_benchmarks/tf_cnn_benchmarks.py --model resnet101 --batch_size 64 --num_batches 5000 --variable_update horovod --train_dir=/training_logs --summary_verbosity=3 --save_summaries_steps=10'
    ```

3.  再次查看集群监控大盘。

    ![jk52](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4627862261/p279124.png)

    由上图可知，集群的GPU的平均利用率、已分配GPU个数与总的GPU数的百分比等指标发生了变化。

4.  执行以下命令，查看任务所在的节点。

    ```
    arena list
    ```

    预期输出：

    ```
    NAME      STATUS   TRAINER  DURATION  GPU(Requested)  GPU(Allocated)  NODE
    mpi-test  RUNNING  MPIJOB   8s        1               1               192.168.1.151
    ```

5.  在集群监控大盘页面，单击右上角的**Nodes**，进入节点监控大盘。由步骤4中得出任务所在的节点IP为`192.168.1.151`，在节点监控大盘上，您可以看到该节点上GPU的相关指标发生的变化如下。

    ![jk53](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4627862261/p279122.png)

6.  在节点监控大盘页面，单击右上角**TrainingJobs**，进入训练任务监控大盘，您可以查看训练任务的相关信息（任务名、任务状态等）。

    ![jk54](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4627862261/p279123.png)


