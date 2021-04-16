---
keyword: [NPU调度, NPU资源]
---

# Kubernetes集群支持NPU调度

通过Kubernetes管理ALI NPU设备，能够帮助您快速部署AI推理任务，监控NPU资源使用情况等。本文主要为您介绍如何使用带有NPU资源的Kubernetes集群。

您需要开通容器服务和访问控制（RAM）服务。

[创建NPU集群](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/创建异构计算集群/创建NPU集群.md)。

与大型算法模型中CPU相比，NPU处理器最明显的优势是信息处理能力快。NPU采用了“数据驱动并行计算”架构，颠覆了CPU所采用的传统冯·诺依曼计算机架构，这种数据流类型的处理器大大提升了计算能力与功耗的比率。NPU特别适合处理视频、图像类的海量多媒体数据的场景，不仅速度比CPU要高出100 ~ 1000倍，同时功耗也远远低于CPU。

您可以通过阿里云Kubernetes集群 + ALI NPU运行机器学习，图像处理等高运算密度等任务，实现快速部署和弹性扩缩容等功能。

**说明：** 如果想了解ALI NPU的相关信息，请参见[AliNPU Website](https://www.t-head.cn/)。

本文将以创建Kubernetes集群时，添加ecs.ebman1.26xlarge实例为例说明如何使用NPU。

## 使用NPU资源

如果某一个Pod需要使用NPU资源，需要在`resources.limits`定义`aliyun.com/npu`的值。

```
apiVersion: v1
kind: Pod
metadata:
  name: <pod名称>
spec:
  containers:
    - name: <容器名称>
      image: <镜像名称>
      resources:
        limits:
          aliyun.com/npu: <请求npu资源数>
```

## 运行TensorFLow的NPU实验环境

您可以在集群中使用NPU资源完成模型训练。本例中，将会启动一个使用NPU资源的Pod进行模型训练。

1.  连接集群，请参见[在CloudShell上通过kubectl管理Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/在CloudShell上通过kubectl管理Kubernetes集群.md)。

    在CloudShell界面执行以下操作。

    ```
    cat > test-pod.yaml <<- EOF
    apiVersion: v1
    kind: Pod
    metadata:
      name: test-npu-pod
    spec:
      restartPolicy: Never
      imagePullSecrets:
        - name: regsecret
      containers:
        - name: resnet50-npu
          image: registry.cn-shanghai.aliyuncs.com/hgai/tensorflow:v1_resnet50-tensorflow1.9.0-toolchain1.0.2-centos7.6
          resources:
            limits:
              aliyun.com/npu: 1 # requesting  NPUs
    EOF
    ```

2.  执行以下命令，创建Pod。

    ```
    kubectl apply -f test-pod.yaml
    ```

3.  执行以下命令，查看Pod的状态。

    ```
    kubectl get po test-npu-pod
    ```

    **说明：** 如果Pod为Error状态，请执行`kubectl logs test-npu-pod`命令监听Pod日志，并排查修改。


等待一段时间后，您可以通过执行以下命令查看Pod的状态。

```
kubectl get po test-npu-pod
```

如果查看到Pod的状态为Completed，再执行以下命令查看日志。

```
kubectl logs test-npu-pod
```

此时看到日志显示结果如下，表示训练任务完成。

```
2019-10-30 12:10:50.389452: I tensorflow/core/platform/cpu_feature_guard.cc:141] Your CPU supports instructions that this TensorFlow binary was not compiled to use: AVX2 AVX512F FMA
100%|##########| 98/98 [00:26<00:00,  3.67it/s]
resnet_v1_50, result =  {'top_5': 0.9244321584701538, 'top_1': 0.7480267286300659}
```

