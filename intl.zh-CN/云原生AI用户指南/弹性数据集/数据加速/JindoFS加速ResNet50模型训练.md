---
keyword: [JindoFS加速ResNet50模型训练, 数据加速, JIndoRuntime]
---

# JindoFS加速ResNet50模型训练

JindoRuntime来源于阿里云EMR团队JindoFS，是基于C++实现的支撑Dataset数据管理和缓存的执行引擎，支持OSS对象存储。Fluid通过管理和调度JIndoRuntime实现数据集的可见性、弹性伸缩和数据迁移。本文介绍如何使用Fluid部署阿里云OSS云端ImageNet数据集到K8s集群，以及如何使用Arena在此数据集上训练ResNet-50模型。

-   已创建ACK Pro版集群，且集群的Kubernetes版本不低于1.14。具体操作，请参见[创建ACK Pro版集群](/intl.zh-CN/Kubernetes集群用户指南/ACK Pro集群/创建ACK Pro版集群.md)。
-   通过kubectl连接Kubernetes集群。具体操作，请参见[t16645.md\#](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl管理Kubernetes集群.md)。
-   在Kubernetes集群中已安装好Fluid，且Fluid版本大于等于0.5.0。具体操作，请参见[Fluid](https://github.com/fluid-cloudnative/fluid)。
-   已安装Arena，且版本大于等于0.6.0。具体操作，请参见[Arena](https://github.com/kubeflow/arena)。
-   已获取Horovod代码，且版本大于等于0.18.1。更多信息，请参见[Horovod](https://github.com/horovod/horovod)。
-   已获取TensorFlow Benchmark代码。更多信息，请参见[Benchmark](https://github.com/tensorflow/benchmarks/tree/cnn_tf_v1.14_compatible)。

## 使用Fluid部署阿里云OSS云端ImageNet数据集到K8s集群

**说明：**

-   如果您希望自己准备数据集，可以访问ImageNet官方网站下载。更多信息，请参见[images](http://image-net.org/download-images)。
-   如果你希望使用我们提供的数据集重现这个实验，请在社区开Issue申请数据集下载。更多信息，请参见[Fluid](https://github.com/fluid-cloudnative/fluid)。

本文以阿里云的V100四机八卡为例，说明如何用Fluid部署阿里云OSS云端ImageNet数据集到K8s集群。

1.  使用以下YAML文件样例创建一个名为dataset.yaml的文件。

    ```
    apiVersion: v1
    kind: Secret
    metadata:
      name: mysecret
    stringData:
      fs.oss.accessKeyId: xxx
      fs.oss.accessKeySecret: xxx
    ---
    apiVersion: data.fluid.io/v1alpha1
    kind: Dataset
    metadata:
      name: imagenet
    spec:
      mounts:
        - mountPoint: oss://<oss_bucket>/<bucket_dir>
          options:
            fs.oss.endpoint: <oss_endpoint>  
          name: imagenet
          encryptOptions:
            - name: fs.oss.accessKeyId
              valueFrom:
                secretKeyRef:
                  name: mysecret
                  key: fs.oss.accessKeyId
            - name: fs.oss.accessKeySecret
              valueFrom:
                secretKeyRef:
                  name: mysecret
                  key: fs.oss.accessKeySecret
      nodeAffinity:   # 配置nodeAffinity的目的是为了保证数据被缓存在V100机器上。
        required:
          nodeSelectorTerms:
            - matchExpressions:
                - key: aliyun.accelerator/nvidia_name
                  operator: In
                  values:
                    - Tesla-V100-SXM2-16GB
    ```

    为保证JindoFS能够成功挂载[阿里云OSS](https://www.alibabacloud.com/zh/product/oss)上的数据集，请确保该YAML文件中设置了正确的mountPoint、fs.oss.accessKeyId、fs.oss.accessKeySecret和fs.oss.endpoint。相关参数解释，请参见[t2057532.md\#table\_0wq\_ba5\_em0](/intl.zh-CN/云原生AI用户指南/弹性数据集/数据加速/JindoFS加速OSS文件访问.md)。

2.  使用以下YAML文件样例创建一个名为runtime的文件。

    ```
    apiVersion: data.fluid.io/v1alpha1
    kind: JindoRuntime
    metadata:
      name: imagenet
    spec:
      replicas: 4
      tieredstore:
        levels:
          - mediumtype: SSD
            path: /var/lib/docker/jindo
            quota: 180G
            high: "0.99"
            low: "0.8"
    ```

    由于该示例以阿里云的V100四机八卡为例，所以JindoRuntim中的`spec.replicas`设置为4。

3.  执行以下命令，分别创建Dataset和JindoRuntime。

    ```
    kubectl create -f dataset.yaml
    kubectl create -f runtime.yaml
    ```

4.  执行以下命令，查看Dataset的部署情况。

    ```
    kubectl get dataset
    ```

    预期输出：

    ```
    NAME     UFS TOTAL SIZE   CACHED   CACHE CAPACITY   CACHED PERCENTAGE   PHASE   AGE
    imagenet   143.67GiB        0.00B    648.00GiB        0.0%                Bound   5
    ```

5.  执行以下命令，查看JindoRuntime的部署情况。

    ```
    kubectl get jindoruntime
    ```

    预期输出：

    ```
    NAME     MASTER PHASE   WORKER PHASE   FUSE PHASE   AGE
    imagenet   Ready          Ready          Ready        4m45s
    ```

6.  执行以下命令，查看PV和PVC的创建情况。

    ```
    kubectl get pv,pvc
    ```

    预期输出：

    ```
    NAME                      CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM            STORAGECLASS   REASON   AGE
    persistentvolume/imagenet   100Gi      RWX            Retain           Bound    default/imagenet                           52m
    
    NAME                           STATUS   VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   AGE
    persistentvolumeclaim/imagenet   Bound    imagenet   100Gi      RWX                           52m
    ```


从上述输出的查询信息，可以知道阿里云OSS云端ImageNet数据集已被成功部署到K8s集群。

## 使用Arena提交深度学习任务

执行以下命令，提交ResNet50四机八卡训练任务。

```
arena submit mpi \
--name horovod-resnet50 \
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

Arena参数说明：

|参数|说明|
|--|--|
|**--name**|指定job的名字。|
|**--workers**|指定参与训练的节点worker数。|
|**--gpus**|指定每个worker使用的GPU数。|
|**--working-dir**|指定工作路径。|
|**--data**|挂载数据卷`imagenet`到节点的/data目录。|
|**-e**|指定数据集位置。|
|**launch-example.sh 4 8**|运行脚本启动四机八卡测试。|

**检查任务是否正常执行**

执行以下命令，检查任务是否正常执行。

```
arena get horovod-resnet50            
```

预期输出：

```
STATUS: RUNNING
NAMESPACE: default
PRIORITY: N/A
TRAINING DURATION: 56m

NAME              STATUS   TRAINER  AGE  INSTANCE                         NODE
horovod-resnet50  RUNNING  MPIJOB   56m  horovod-resnet50-launcher-xs7h4  192.168.0.15
horovod-resnet50  RUNNING  MPIJOB   56m  horovod-resnet50-worker-0        192.168.0.15
horovod-resnet50  RUNNING  MPIJOB   56m  horovod-resnet50-worker-1        192.168.0.14
horovod-resnet50  RUNNING  MPIJOB   56m  horovod-resnet50-worker-2        192.168.0.12
horovod-resnet50  RUNNING  MPIJOB   56m  horovod-resnet50-worker-3        192.168.0.13
```

从上述输出信息，可以知道K8s已经成功启动ResNet-50模型训练。

**查看Arena的日志信息**

执行以下命令，查看Arena的日志信息。

```
arena logs --tail 100 -f horovod-resnet50
```

## 性能对比

使用Fluid部署阿里云OSS云端ImageNet数据集到K8s集群，并使用Arena在此数据集上训练ResNet-50模型，基于JindoFS的JindoRuntime在开启本地缓存的情况下性能大幅度优于开源OSSFS，训练耗时缩短了76%。

## 环境清理

当您不再使用该数据加速功能时，需要清理环境。

执行以下命令，删除Dataset和JindoRuntime。

```
kubectl delete -f dataset imagenet
kubectl delete -f jindoruntime imagenet
```

