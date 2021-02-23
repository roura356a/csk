---
keyword: [Fluid, 加速机器学习训练]
---

# 配置Fluid加速机器学习训练

Fluid是一个开源的Kubernetes原生的分布式数据集编排和加速引擎，主要服务于云原生场景下的数据密集型应用，例如大数据应用、AI应用等。本文介绍如何使用Fluid部署阿里云OSS云端ImageNET数据集到Kubernetes集群，并使用Arena在此数据集上训练ResNet-50模型。

-   在Kubernetes集群中已安装好Fluid（版本高于0.5.0）。具体操作，请参见[Fluid](https://github.com/fluid-cloudnative/fluid)。
-   已安装Arena（版本高于0.5.0）。具体操作，请参见[Arena](https://github.com/kubeflow/arena)。
-   已获取Horovod代码。代码详情，请参见[Horovod](https://github.com/horovod/horovod)。
-   已获取TensorFlow Benchmark代码。代码详情，请参见[Benchmark](https://github.com/tensorflow/benchmarks/tree/cnn_tf_v1.14_compatible)。
-   安装[kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)命令行工具。

## Fluid功能介绍

Fluid是一个开源的Kubernetes原生的分布式数据集编排和加速引擎，主要服务于云原生场景下的数据密集型应用，例如大数据应用、AI应用等。通过定义数据集资源的抽象，实现如下图功能：

![ai-1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0037682161/p233036.png)

## 核心概念

Fluid的核心概念如下：

-   数据集抽象原生支持：将数据密集型应用所需基础支撑能力功能化，实现数据高效访问并降低多维管理成本。
-   云上数据预热与加速：Fluid通过使用分布式缓存引擎（Alluxio inside）为云上应用提供数据预热与加速，同时可以保障缓存数据的可观测性，可迁移性和自动化的水平扩展。
-   数据应用协同编排：在云上调度应用和数据时，同时考虑两者特性与位置，实现协同编排，提升性能。
-   多命名空间管理支持：用户可以创建和管理不同Namespace的数据集。
-   异构数据源管理：一次性统一访问不同来源的底层数据（对象存储，HDFS和Ceph等存储\)，适用于混合云场景。

## 重要概念

Fluid的重要概念如下：

-   Dataset：数据集是逻辑上相关的一组数据的集合，会被运算引擎使用。例如，大数据的Spark，AI场景的TensorFlow。而这些数据智能的应用会创造工业界的核心价值。Dataset的管理实际上也有多个维度，例如安全性，版本管理和数据加速。我们希望从数据加速出发，对于数据集的管理提供支持。
-   Runtime：实现数据集安全性，版本管理和数据加速等能力的执行引擎，定义了一系列生命周期的接口。可以通过实现这些接口，支持数据集的管理和加速。
-   AlluxioRuntime：来源于[Alluixo](https://www.alluxio.org/)社区，是支撑Dataset数据管理和缓存的执行引擎实现，支持PVC，Ceph，CPFS计算，有效支持混合云场景。但是Alluxio是开源社区方案，对于数据缓存的稳定性和性能优化会和社区一起推动，但是时效性和响应会有延时。Fluid通过管理和调度Alluxio Runtime实现数据集的可见性，弹性伸缩，数据迁移。

|特点|Alluxio|
|--|-------|
|底层存储类型|PVC，Ceph，HDFS，CPFS，NFS等|
|支持方式|开源社区|
|实现语言|Java|

## 用Fluid加速机器学习训练

本文介绍如何使用Fluid部署[阿里云OSS](https://cn.aliyun.com/product/oss)云端[ImageNet](http://www.image-net.org/)数据集到Kubernetes集群，并使用[Arena](https://github.com/kubeflow/arena)在此数据集上训练ResNet-50模型。

**用Fluid部署云端数据集：创建Dataset和Runtime**

以下的dataset.yaml文件中定义了一个`Dataset`和`Runtime`，并使用`---`符号将它们分割。

数据集存储在[阿里云OSS](https://cn.aliyun.com/product/oss)，为保证Alluxio能够成功挂载OSS上的数据集，请确保dataset.yaml文件中设置了正确的`mountPoint`、`fs.oss.accessKeyId`、`fs.oss.accessKeySecret`和`fs.oss.endpoint`。

**说明：**

-   若您自己准备数据集，可以在[ImageNet官方网站](http://image-net.org/download-images)获取。
-   若您要使用我们提供的数据集，请[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)申请。

本文档以阿里云的V100四机八卡为例，所以在dataset.yaml中设置`spec.replicas=4`。为了保证数据被缓存在V100机器上，配置`nodeAffinity`。此外，在dataset.yaml文件，可以自行根据机器配置和任务需求调整参数。也可以参照数据集管理文档创建Dataset。

```
cat << EOF >> dataset.yaml
apiVersion: data.fluid.io/v1alpha1
kind: Dataset
metadata:
  name: imagenet
spec:
  mounts:
    - mountPoint: oss://chengli-sh-test.oss-cn-shanghai-internal.aliyuncs.com/
      name: imagenet
      options:
        fs.oss.accessKeyId: xxx
        fs.oss.accessKeySecret: xxx
  nodeAffinity:
    required:
      nodeSelectorTerms:
        - matchExpressions:
            - key: aliyun.accelerator/nvidia_name
              operator: In
              values:
                - Tesla-V100-SXM2-16GB
---
apiVersion: data.fluid.io/v1alpha1
kind: AlluxioRuntime
metadata:
  name: imagenet
spec:
  replicas: 4
  tieredstore:
    levels:
      - mediumtype: MEM
        path: /dev/shm
        quota: 100Gi
        high: "0.99"
        low: "0.8"
EOF
```

1.  执行以下命令，创建Dataset和Runtime。

    ```
    kubectl create -f dataset.yaml
    ```

2.  执行以下命令，检查Jindo Runtime。

    ```
    kubectl describe alluxioruntime imagenet 
    ```

    预期输出：

    ```
    Name:         imagenet
    Namespace:    default
    Labels:       <none>
    Annotations:  <none>
    API Version:  data.fluid.io/v1alpha1
    Kind:         AlluxioRuntime
    Metadata:
      # more metadata
    Spec:
      # more spec
    Status:
      Cache States:
        Cache Capacity:     200GiB
        Cached:             0B
        Cached Percentage:  0%
      Conditions:
        # more conditions
      Current Fuse Number Scheduled:    4
      Current Master Number Scheduled:  1
      Current Worker Number Scheduled:  4
      Desired Fuse Number Scheduled:    4
      Desired Master Number Scheduled:  1
      Desired Worker Number Scheduled:  4
      Fuse Number Available:            4
      Fuse Numb    Status:                True
        Type:                  Ready
      Phase:                   Bound
      Runtimes:
        Category:   Accelerate
        Name:       imagenet
        Namespace:  default
        Type:       alluxio
      Ufs Total:    143.7GiB
    Events:         <none>
    ```

    根据以上预期输出，可以看到已成功部署1个Master，4个Worker和4个Fuse。

3.  执行以下命令，检查Dataset。

    ```
    kubectl describe dataset
    ```

    预期输出：

    ```
    Name:         imagenet
    Namespace:    default
    Labels:       <none>
    Annotations:  <none>
    API Version:  data.fluid.io/v1alpha1
    Kind:         Dataset
    Metadata:
      # more metadata
    Spec:
      # more spec
    Status:
      Cache States:
        Cache Capacity:     200GiB
        Cached:             0B
        Cached Percentage:  0%
      Conditions:
        Last Transition Time:  2020-08-18T11:01:09Z
        Last Update Time:      2020-08-18T11:02:48Z
        Message:               The ddc runtime is ready.
        Reason:                DatasetReady
        Status:                True
        Type:                  Ready
      Phase:                   Bound
      Runtimes:
        Category:   Accelerate
        Name:       imagenet
        Namespace:  default
        Type:       alluxio
      Ufs Total:    143.7GiB
    Events:         <none>
    ```

    根据以上预期输出，可以看到Alluxio Runtime已成功部署Dataset。

4.  执行以下命令，检查PV和PVC。

    ```
    kubectl get pv,pvc
    ```

    预期输出：

    ```
    NAME                        CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM              STORAGECLASS   REASON   AGE
    persistentvolume/imagenet   100Gi      RWX            Retain           Bound    default/imagenet                           7m11s
    
    NAME                             STATUS   VOLUME     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
    persistentvolumeclaim/imagenet   Bound    imagenet   100Gi      RWX                           7m11s
    ```

    根据以上预期输出，可以看到已成功创建名为`imagenet`的`persistentvolume`和`persistentvolumeclaim`。


## 使用Arena提交深度学习任务

Arena提供了便捷的方式帮助用户提交和监控机器学习任务。提交训练任务前，需要将云端数据集成功部署到本地集群中。在本文中，我们使用Arena简化机器学习任务的部署流程。

1.  执行以下命令，使用Arena提交ResNet50四机八卡训练任务。

    ```
    arena submit mpi \
        --name horovod-resnet50-v2-4x8-fluid \
        --gpus=8 \
        --workers=4 \
        --working-dir=/horovod-demo/tensorflow-demo/ \
        --data imagenet:/data \
        -e DATA_DIR=/data/imagenet \
        -e num_batch=1000 \
        -e datasets_num_private_threads=8 \
        --image=registry.cn-hangzhou.aliyuncs.com/tensorflow-samples/horovod-benchmark-dawnbench-v2:0.18.1-tf1.14.0-torch1.2.0-mxnet1.5.0-py3.6 \
        ./launch-example.sh 4 8
    ```

    **说明：** Arena参数说明：

    -   `--name`：指定job的名称。
    -   `--workers`：指定参与训练的节点（worker）数。
    -   `--gpus`：指定每个worker使用的GPU数。
    -   `--working-dir`：指定工作路径。
    -   `--data`：挂载Volume imagenet到worker的/data目录。
    -   `-e DATA_DIR`：指定数据集位置。
    -   `./launch-example.sh 4 8`：运行脚本启动四机八卡测试。
2.  执行以下命令，查看训练任务是否正常执行。

    ```
    arena get horovod-resnet50-v2-4x8-fluid -e
    ```

    预期输出：

    ```
    STATUS: RUNNING
    NAMESPACE: default
    PRIORITY: N/A
    TRAINING DURATION: 16s
    
    NAME                           STATUS   TRAINER  AGE  INSTANCE                                      NODE
    horovod-resnet50-v2-4x8-fluid  RUNNING  MPIJOB   16s  horovod-resnet50-v2-4x8-fluid-launcher-czlfn  192.168.XX.XX
    horovod-resnet50-v2-4x8-fluid  RUNNING  MPIJOB   16s  horovod-resnet50-v2-4x8-fluid-worker-0        192.168.XX.XX
    horovod-resnet50-v2-4x8-fluid  RUNNING  MPIJOB   16s  horovod-resnet50-v2-4x8-fluid-worker-1        192.168.XX.XX
    horovod-resnet50-v2-4x8-fluid  RUNNING  MPIJOB   16s  horovod-resnet50-v2-4x8-fluid-worker-2        192.168.XX.XX
    horovod-resnet50-v2-4x8-fluid  RUNNING  MPIJOB   16s  horovod-resnet50-v2-4x8-fluid-worker-3        192.168.XX.XX
    ```

    根据以上预期输出，可以看到4个处于RUNNING状态的worker，说明您已经成功启动训练。


**说明：** 若需要了解当前训练任务进行阶段，可执行以下命令，查看Arena日志。

```
arena logs --tail 100 -f horovod-resnet50-v2-4x8-fluid
```

## 性能比较

通过在单机八卡及四机八卡场景下，有无Fluid加速的对比，可以得出各个场景下的训练时间。具体场景的操作，请参见[示例 - 使用Fluid加速主机目录](https://github.com/fluid-cloudnative/fluid/blob/6b8ba95698a79890cd6e6032cd554eb724f42212/docs/zh/samples/hostpath.md)。

|配置场景|nfs-hostpath|fluid \(cold\)|fluid \(warm\)|
|----|------------|--------------|--------------|
|单机八卡|4小时20分钟36秒|4小时21分钟56秒|4小时2分钟16秒|
|四机八卡|2小时9分钟21秒|1小时40分钟15秒|1小时29分钟55秒|

结果分析：

-   单机八卡通过Fluid加速效果并没有明显的效果，但是在四机八卡的场景下Fluid加速效果非常明显。

    **说明：** 这是由于四机八卡下，NFS的带宽成为了瓶颈，而Fluid基于Alluxio提供了分布式缓存的P2P数据读取能力。

-   在热数据场景下，可以缩短约31%的训练时间。
-   在冷数据场景下，可以缩短约22%的训练时间 。

## 环境清理

当您不再使用分布式数据加速功能时，需要清理环境。

执行以下命令，删除训练任务。

```
arena delete horovod-resnet50-v2-4x8-fluid
```

预期输出：

```
mpijob.kubeflow.org "horovod-resnet50-v2-4x8-fluid" deleted
configmap "horovod-resnet50-v2-4x8-fluid-mpijob" deletedINFO[0010] The training job horovod-resnet50-v2-4x8-fluid has been deleted successfully
```

