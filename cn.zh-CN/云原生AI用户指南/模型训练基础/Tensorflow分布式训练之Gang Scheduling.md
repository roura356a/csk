---
keyword: Gang Scheduling
---

# Tensorflow分布式训练之Gang Scheduling

Gang Scheduling是在并发系统中将多个相关联的进程调度到不同处理器上同时运行。为了防止部分进程异常，导致整个关联进程组的阻塞，本文介绍Gang Scheduling功能的操作示例。

-   已安装云原生AI套件，并选中**调度组件**。具体操作，请参见[安装云原生AI套件及访问AI Dashbaord](/cn.zh-CN/云原生AI用户指南/安装云原生AI套件及访问AI Dashbaord.md)。
-   已安装Arena客户端。具体详情，请参见[t1917487.dita\#task\_1917487](/cn.zh-CN/解决方案/AI解决方案/环境准备/通过组件安装最新版的Arena.md)。

Gang scheduling是在并发系统中将多个相关联的进程调度到不同处理器上同时运行的策略，其最主要的原则是保证所有相关联的进程能够同时启动，防止部分进程的异常，导致整个关联进程组的阻塞。例如，您提交一个批量Job，这个批量Job包含多个任务，要么这多个任务全部调度成功，要么一个都调度不成功。这种All-or-Nothing调度场景，就被称作Gang scheduling。

Kubernetes目前已经广泛的应用于在线服务编排，为了提升集群的利用率和运行效率，ACK希望将Kubernetes作为一个统一的管理平台来管理在线服务和离线作业。但是由于调度器的限制，使得一些离线的工作负载无法迁移到Kubernetes。例如对于有All-or-Nothing特点的作业，它要求所有的任务在同一时间被调度，如果只是部分任务启动的话，启动的任务将持续等待剩余的任务被调度。在最坏的情况下，所有作业都处于挂起状态，从而导致死锁。为了解决这个问题，需要对调度器支持Gang scheduling。

## 操作示例

**说明：**

-   通过Arena提交将gang设置为`true`，可开启Gang Scheduling功能。
-   本示例通过运行Tensorflow的分布式作业体现Gang Scheduling的功能。
-   本示例集群的GPU数为8。

-   当开启Gang时，向集群提交5个Worker的作业。

    每个Worker需要2个GPU，此时由于集群总资源不满足分布式作业的需求，则全部处于Pending状态。

    代码示例如下所示：

    ```
    arena submit mpi \
        --name=mpi-dist \
        --gpus=2 \
        --workers=5 \
        --gang=true \
        --image=uber/horovod:0.13.11-tf1.10.0-torch0.4.0-py3.5 \
        --env=GIT_SYNC_BRANCH=cnn_tf_v1.9_compatible \
        --sync-mode=git \
        --sync-source=https://github.com/tensorflow/benchmarks.git \
        --tensorboard \
        "mpirun python code/benchmarks/scripts/tf_cnn_benchmarks/tf_cnn_benchmarks.py --model resnet101 --batch_size 64 --variable_update horovod --train_dir=/training_logs --summary_verbosity=3 --save_summaries_steps=10"
    ```

    预期结果：

    ```
    arena get mpi-dist
    Instances:
      NAME               STATUS   AGE  IS_CHIEF  GPU(Requested)  NODE
      ----               ------   ---  --------  --------------  ----
      mpi-dist-worker-0  Pending  20s  false     2               N/A
      mpi-dist-worker-1  Pending  20s  false     2               N/A
      mpi-dist-worker-2  Pending  20s  false     2               N/A
      mpi-dist-worker-3  Pending  20s  false     2               N/A
      mpi-dist-worker-4  Pending  20s  false     2               N/A
    ```

-   当开启Gang时，向集群提交4个Worker的作业。

    每个Worker需要2个GPU，此时由于集群总资源满足分布式作业的需求，则完成调度，Pod创建成功。

    代码示例如下所示：

    ```
    arena submit mpi \
        --name=mpi-dist \
        --gpus=2 \
        --workers=4 \
        --gang=true \
        --image=uber/horovod:0.13.11-tf1.10.0-torch0.4.0-py3.5 \
        --env=GIT_SYNC_BRANCH=cnn_tf_v1.9_compatible \
        --sync-mode=git \
        --sync-source=https://github.com/tensorflow/benchmarks.git \
        --tensorboard \
        "mpirun python code/benchmarks/scripts/tf_cnn_benchmarks/tf_cnn_benchmarks.py --model resnet101 --batch_size 64 --variable_update horovod --train_dir=/training_logs --summary_verbosity=3 --save_summaries_steps=10"
    ```

    预期结果：

    ```
    arena get mpi-dist
    Instances:
      NAME                     STATUS   AGE  IS_CHIEF  GPU(Requested)  NODE
      ----                     ------   ---  --------  --------------  ----
      mpi-dist-launcher-5279n  Running  5m   true      0               cn-guangzhou.192.168.XX.XX
      mpi-dist-worker-0        Running  5m   false     2               cn-guangzhou.192.168.XX.XX
      mpi-dist-worker-1        Running  5m   false     2               cn-guangzhou.192.168.XX.XX
      mpi-dist-worker-2        Running  5m   false     2               cn-guangzhou.192.168.XX.XX
      mpi-dist-worker-3        Running  5m   false     2               cn-guangzhou.192.168.XX.XX
    ```


