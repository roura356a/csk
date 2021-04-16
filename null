---
keyword: [Gang scheduling, 资源调度, 协同调度]
---

# Gang scheduling

ACK基于新版的Kube-scheduler框架实现Gang scheduling的能力，解决原生调度器对于All-or-Nothing作业调度的问题。本文举例说明如何使用Gang scheduling能力。

-   您已创建ACK Pro版集群。具体步骤，请参见[创建ACK Pro版集群](/intl.zh-CN/Kubernetes集群用户指南/ACK Pro集群/创建ACK Pro版集群.md)。

    **说明：** 目前Gang scheduling仅支持ACK Pro托管版集群。如果您需要专有版集群，请[提交工单](https://workorder-intl.console.aliyun.com/console.htm)申请白名单。

-   系统组件版本要求具体如下表所示。

    |组件|版本要求|
    |--|----|
    |Kubernetes|1.16及以上|
    |Helm版本|3.0及以上版本|
    |Docker版本|19.03.5|
    |操作系统|CentOS 7.6、CentOS 7.7、Ubuntu 16.04、Ubuntu 18.04、Alibaba Cloud Linux 2|


Gang scheduling是在并发系统中将多个相关联的进程调度到不同处理器上同时运行的策略，其最主要的原则是保证所有相关联的进程能够同时启动，防止部分进程的异常，导致整个关联进程组的阻塞。例如，您提交一个批量Job，这个批量Job包含多个任务，要么这多个任务全部调度成功，要么一个都调度不成功。这种All-or-Nothing调度场景，就被称作Gang scheduling。

Kubernetes目前已经广泛的应用于在线服务编排，为了提升集群的利用率和运行效率，ACK希望将Kubernetes作为一个统一的管理平台来管理在线服务和离线作业。但是由于调度器的限制，使得一些离线的工作负载无法迁移到Kubernetes。例如对于有All-or-Nothing特点的作业，它要求所有的任务在同一时间被调度，如果只是部分任务启动的话，启动的任务将持续等待剩余的任务被调度。在最坏的情况下，所有作业都处于挂起状态，从而导致死锁。为了解决这个问题，需要对调度器支持Gang scheduling。

## 功能介绍

ACK将一组需要同时调度的Pod称为PodGroup。您如果在提交All-or-Nothing作业时，可以通过设置labels字段的形式指定所属PodGroup的名称以及保证作业正常运行Task的最少运行个数。调度器会根据您指定的最少运行个数进行调度，只有当集群资源满足该Task最少运行个数时，才会统一调度，否则作业将一直处于Pending状态。

## 使用方式

使用Gang scheduling时，在创建的Pod处通过设置labels的形式配置min-available和name。

```
labels:
    pod-group.scheduling.sigs.k8s.io/name: tf-smoke-gpu
    pod-group.scheduling.sigs.k8s.io/min-available: "3"
```

-   name：用于表示PodGroup的名称。
-   min-available：用于表示当前集群资源至少满足最少可运行Pod数时，才能统一调度创建。

**说明：** 要求属于同一个PodGroup的Pod必须保持相同的优先级。

## 示例

文本通过运行Tensorflow的分布式作业来展示Gang scheduling的效果。当前测试集群有4个GPU卡。

1.  执行以下命令通过Kubeflow的[Arena](https://github.com/kubeflow/arena)工具在已有Kubernetes集群中部署Tensorflow作业运行环境。

    **说明：** Arena是基于Kubernetes的机器学习系统开源社区[Kubeflow](https://github.com/kubeflow)中的子项目之一。Arena用命令行和SDK的形式支持了机器学习任务的主要生命周期管理（包括环境安装、数据准备，到模型开发、模型训练、模型预测等），有效提升了数据科学家工作效率。

    ```
    git clone https://github.com/kubeflow/arena.git
    kubectl create ns arena-system
    kubectl create -f arena/kubernetes-artifacts/jobmon/jobmon-role.yaml
    kubectl create -f arena/kubernetes-artifacts/tf-operator/tf-crd.yaml
    kubectl create -f arena/kubernetes-artifacts/tf-operator/tf-operator.yaml
    ```

    执行以下命令检查Tensorflow作业运行环境是否成功部署。当出现Running状态时，说明成功部署。

    ```
    kubectl  get pods -n arena-system
    ```

    ```
    NAME                                READY   STATUS    RESTARTS   AGE
    tf-job-dashboard-56cf48874f-gwlhv   1/1     Running   0          54s
    tf-job-operator-66494d88fd-snm9m    1/1     Running   0          54s
    ```

2.  使用以下模板向集群中提交Tensorflow分布式作业，含有1个PS和4个Worker，每个Worker类型的Pod需要2个GPU。

    ```
    apiVersion: "kubeflow.org/v1"
    kind: "TFJob"
    metadata:
      name: "tf-smoke-gpu"
    spec:
      tfReplicaSpecs:
        PS:
          replicas: 1
          template:
            metadata:
              creationTimestamp: null
              labels:
                pod-group.scheduling.sigs.k8s.io/name: tf-smoke-gpu
                pod-group.scheduling.sigs.k8s.io/min-available: "5"
            spec:
              containers:
              - args:
                - python
                - tf_cnn_benchmarks.py
                - --batch_size=32
                - --model=resnet50
                - --variable_update=parameter_server
                - --flush_stdout=true
                - --num_gpus=1
                - --local_parameter_device=cpu
                - --device=cpu
                - --data_format=NHWC
                image: registry.cn-hangzhou.aliyuncs.com/kubeflow-images-public/tf-benchmarks-cpu:v20171202-bdab599-dirty-284af3
                name: tensorflow
                ports:
                - containerPort: 2222
                  name: tfjob-port
                resources:
                  limits:
                    cpu: '1'
                workingDir: /opt/tf-benchmarks/scripts/tf_cnn_benchmarks
              restartPolicy: OnFailure
        Worker:
          replicas: 4
          template:
            metadata:
              creationTimestamp: null
              labels:
                pod-group.scheduling.sigs.k8s.io/name: tf-smoke-gpu
                pod-group.scheduling.sigs.k8s.io/min-available: "5"
            spec:
              containers:
              - args:
                - python
                - tf_cnn_benchmarks.py
                - --batch_size=32
                - --model=resnet50
                - --variable_update=parameter_server
                - --flush_stdout=true
                - --num_gpus=1
                - --local_parameter_device=cpu
                - --device=gpu
                - --data_format=NHWC
                image: registry.cn-hangzhou.aliyuncs.com/kubeflow-images-public/tf-benchmarks-gpu:v20171202-bdab599-dirty-284af3
                name: tensorflow
                ports:
                - containerPort: 2222
                  name: tfjob-port
                resources:
                  limits:
                    nvidia.com/gpu: 2
                workingDir: /opt/tf-benchmarks/scripts/tf_cnn_benchmarks
              restartPolicy: OnFailure
    ```

    -   不使用Gang scheduling功能

        执行以下命令查看Pod状态。集群中只用2个Worker类型的Pod在运行，剩余2个处于Pending状态。

        ```
        kubectl get pods
        ```

        ```
        NAME                    READY   STATUS    RESTARTS   AGE
        tf-smoke-gpu-ps-0       1/1     Running   0          6m43s
        tf-smoke-gpu-worker-0   1/1     Running   0          6m43s
        tf-smoke-gpu-worker-1   1/1     Running   0          6m43s
        tf-smoke-gpu-worker-2   0/1     Pending   0          6m43s
        tf-smoke-gpu-worker-3   0/1     Pending   0          6m43s
        ```

        执行以下命令查看其中正在运行的Worker类型Pod的日志。处于等待剩余两个Worker类型Pod启动的状态，GPU占用却没有使用。

        ```
        kubectl logs -f tf-smoke-gpu-worker-0
        ```

        ```
        INFO|2020-05-19T07:02:18|/opt/launcher.py|27| 2020-05-19 07:02:18.199696: I tensorflow/core/distributed_runtime/master.cc:221] CreateSession still waiting for response from worker: /job:worker/replica:0/task:3
        INFO|2020-05-19T07:02:28|/opt/launcher.py|27| 2020-05-19 07:02:28.199798: I tensorflow/core/distributed_runtime/master.cc:221] CreateSession still waiting for response from worker: /job:worker/replica:0/task:2
        ```

    -   使用Gang scheduling功能时

        执行以下命令查看Pod状态。因为集群的资源无法满足设定的最小数要求，则PodGroup无法正常调度，所有的Pod一直处于Pending状态。

        ```
        kubectl get pods
        ```

        ```
        NAME                    READY   STATUS    RESTARTS   AGE
        tf-smoke-gpu-ps-0       0/1     Pending   0          43s
        tf-smoke-gpu-worker-0   0/1     Pending   0          43s
        tf-smoke-gpu-worker-1   0/1     Pending   0          43s
        tf-smoke-gpu-worker-2   0/1     Pending   0          43s
        tf-smoke-gpu-worker-3   0/1     Pending   0          43s
        ```

        当集群中新增4个GPU卡的资源，则当前集群的资源满足设定的最小数要求，则PodGroup正常调度，4个Worker类型Pod开始运行。执行以下命令查看Pod状态。

        ```
        kubectl get pods
        ```

        ```
        NAME                    READY   STATUS    RESTARTS   AGE
        tf-smoke-gpu-ps-0       1/1     Running   0          3m16s
        tf-smoke-gpu-worker-0   1/1     Running   0          3m16s
        tf-smoke-gpu-worker-1   1/1     Running   0          3m16s
        tf-smoke-gpu-worker-2   1/1     Running   0          3m16s
        tf-smoke-gpu-worker-3   1/1     Running   0          3m16s
        ```

        执行以下命令查看其中一个Worker类型Pod的日志，显示训练任务已经开始。

        ```
        kubectl logs -f tf-smoke-gpu-worker-0
        ```

        ```
        INFO|2020-05-19T07:15:24|/opt/launcher.py|27| Running warm up
        INFO|2020-05-19T07:21:04|/opt/launcher.py|27| Done warm up
        INFO|2020-05-19T07:21:04|/opt/launcher.py|27| Step  Img/sec loss
        INFO|2020-05-19T07:21:05|/opt/launcher.py|27| 1 images/sec: 31.6 +/- 0.0 (jitter = 0.0) 8.318
        INFO|2020-05-19T07:21:15|/opt/launcher.py|27| 10  images/sec: 31.1 +/- 0.4 (jitter = 0.7) 8.343
        INFO|2020-05-19T07:21:25|/opt/launcher.py|27| 20  images/sec: 31.5 +/- 0.3 (jitter = 0.7) 8.142
        ```


