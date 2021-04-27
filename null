---
keyword: [GPU, 弹性伸缩, external metrics, HPA]
---

# 基于GPU指标实现弹性伸缩

Kubernetes提供了External Metrics机制，该机制可以对接阿里云Prometheus监控来采集GPU指标。本文介绍如何部署阿里云Prometheus监控，然后举例说明如何通过阿里云Prometheus监控观测GPU指标，实现容器的弹性伸缩。

您已完成创建以下任一类型集群：

-   [创建托管GPU集群](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/创建异构计算集群/创建托管GPU集群.md)
-   [创建专有GPU集群](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/创建异构计算集群/创建专有GPU集群.md)

在高性能计算领域，比如深度学习模型训练、推理等场景，通常需要使用GPU来做计算加速。为了节省成本，您往往需要根据GPU指标（利用率、显存）来做弹性伸缩。虽然默认的HPA（Horizontal Pod Autoscaler）组件并不支持根据GPU指标实现弹性伸缩，但Kubernetes提供的[External Metrics机制](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/#autoscaling-on-metrics-not-related-to-kubernetes-objects)可以通过GPU指标实现容器弹性伸缩。

![hpa](../images/p174927.jpeg "GPU弹性伸缩原理图")

## 步骤一：部署阿里云Prometheus

1.  安装阿里云Prometheus监控。

    您有三种安装阿里云Prometheus监控的方式。有关具体操作，请参见[开启阿里云Prometheus监控](/cn.zh-CN/Kubernetes集群用户指南/可观测性/监控管理/阿里云Prometheus监控.md)。

2.  配置ack-alibaba-cloud-metrics-adapter。

    1.  登录[阿里云Prometheus控制台](https://prometheus.console.aliyun.com/#/prom/cn-hangzhou)。

    2.  在控制台左侧导航栏中，单击**监控列表**，然后在Prometheus监控页面单击目标集群名称。

    3.  在左侧导航栏，单击**设置**。

    4.  单击**Agent设置**页签，在**健康检查结果**区域中找到**步骤2：api接口地址**。

        ![Agent](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8582973061/p176933.png)

    5.  在[容器服务管理控制台](https://cs.console.aliyun.com)左侧导航栏中，单击**市场** \> **应用目录**。

    6.  在应用目录页面，找到并单击**ack-alibaba-cloud-metrics-adapter**。

    7.  在**应用目录- ack-alibaba-cloud-metrics-adapter**页面，单击**参数**页签，然后将上述获取的[api接口地址](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/可观测性/配置GPU Prometheus监控.md)设置为**url**的值。

        ![url](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6169986061/p176954.png)

    8.  在**创建**面板选择目标集群，单击**创建**。


## 步骤二：基于GPU指标实现弹性伸缩

本文通过在GPU上部署一个模型推理服务，然后对其进行压测。根据GPU利用率测试弹性伸缩。

1.  部署推理服务。

    1.  使用以下YAML文件内容部署推理服务。

        ```
        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: bert-intent-detection
        spec:
          replicas: 1
          selector:
            matchLabels:
              app: bert-intent-detection
          template:
            metadata:
              labels:
                app: bert-intent-detection
            spec:
              containers:
              - name: bert-container
                image: registry.cn-hangzhou.aliyuncs.com/ai-samples/bert-intent-detection:1.0.1
                ports:
                - containerPort: 80
                resources:
                  limits:
                    nvidia.com/gpu: 1
        ```

    2.  执行以下命令查看Pod和Service。

        查看Pod命令：

        ```
        kubectl get pods -o wide
        ```

        预期输出：

        ```
        NAME                                    READY   STATUS    RESTARTS   AGE     IP             NODE                       NOMINATED NODE   READINESS GATES
        bert-intent-detection-7b6667bd9-kgx2g   1/1     Running   0          8m58s   172.20.0.207   cn-beijing.192.168.0.114   <none>           <none>
        ```

        上述输出可以看到当前只有一个Pod部署在192.168.0.115这个GPU节点上。

        查看Service命令：

        ```
        kubectl get svc bert-intent-detection-svc
        ```

        预期输出：

        ```
        NAME                        TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)          AGE
        bert-intent-detection-svc   LoadBalancer   172.21.5.80   101.200.xx.xx 8500:32599/TCP   9m16s
        ```

    3.  通过SSH登录节点192.168.0.114后，执行以下命令查看GPU使用情况。

        ```
        nvidia-smi
        ```

        预期输出：

        ```
        Thu Oct 15 15:41:50 2020
        +-----------------------------------------------------------------------------+
        | NVIDIA-SMI 418.87.01    Driver Version: 418.87.01    CUDA Version: 10.1     |
        |-------------------------------+----------------------+----------------------+
        | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
        | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
        |===============================+======================+======================|
        |   0  Tesla T4            On   | 00000000:00:07.0 Off |                    0 |
        | N/A   45C    P0    28W /  70W |  14315MiB / 15079MiB |      0%      Default |
        +-------------------------------+----------------------+----------------------+
        
        +-----------------------------------------------------------------------------+
        | Processes:                                                       GPU Memory |
        |  GPU       PID   Type   Process name                             Usage      |
        |=============================================================================|
        |    0   1800353      C   python                                     14305MiB |
        +-----------------------------------------------------------------------------+
        ```

        推理服务进程已经运行在GPU上，当前GPU利用率为0。

    4.  执行以下命令调用推理服务，测试是否部署成功。

        ```
        curl -v "http://101.200.xx.xx:8500/predict?query=music"
        ```

        预期输出：

        ```
        *   Trying 101.200.xx.xx...
        * TCP_NODELAY set
        * Connected to 101.200.xx.xx (101.200.xx.xx) port 8500 (#0)
        > GET /predict?query=music HTTP/1.1
        > Host: 101.200.xx.xx:8500
        > User-Agent: curl/7.64.1
        > Accept: */*
        >
        * HTTP 1.0, assume close after body
        < HTTP/1.0 200 OK
        < Content-Type: text/html; charset=utf-8
        < Content-Length: 9
        < Server: Werkzeug/1.0.1 Python/3.6.9
        < Date: Thu, 15 Oct 2020 07:42:43 GMT
        <
        * Closing connection 0
        PlayMusic #意图识别结果
        ```

        当HTTP请求返回状态码200时，说明成功部署推理服务。

2.  配置HPA。

    1.  使用以下YAML信息部署HPA。

        ```
        apiVersion: autoscaling/v2beta1
        kind: HorizontalPodAutoscaler
        metadata:
          name: gpu-hpa
        spec:
          scaleTargetRef:
            apiVersion: apps/v1
            kind: Deployment
            name: bert-intent-detection
          minReplicas: 1
          maxReplicas: 10
          metrics:
          - type: Pods
            pods:
              metricName: duty_cycle_current # 指标为Pod的平均GPU使用率。
              targetAverageValue: 20
        ```

    2.  执行以下命令查看HPA。

        ```
        kubectl get hpa
        ```

        预期输出：

        ```
        NAME      REFERENCE                          TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
        gpu-hpa   Deployment/bert-intent-detection   0/20      1         10        1          11m
        ```

        从TARGETS中可以看到，当前GPU利用率是0，当利用率为20时触发弹性伸缩。

3.  执行以下命令进行压测。

    ```
    hey -n 10000 -c 100 "http://101.200.88.51:8500/predict?query=music"
    ```

    1.  压测过程中，执行以下命令观察HPA的状态。

        ```
        kubectl get hpa
        ```

        预期输出：

        ```
        NAME      REFERENCE                          TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
        gpu-hpa   Deployment/bert-intent-detection   22/20     1         10        1          13m
        ```

    2.  当TARGETS中当前GPU利用率超过20时，触发弹性伸缩，此时ACK集群开始扩容。执行以下命令查看伸缩情况。

        ```
        kubectl get pods
        ```

        预期输出：

        ```
        NAME                                    READY   STATUS              RESTARTS   AGE
        bert-intent-detection-7b6667bd9-kgx2g   1/1     Running             0          13m
        bert-intent-detection-7b6667bd9-zrvvr   0/1     ContainerCreating   0          21s
        ```

        **说明：** 当压测停止，GPU利用率降低且低于20后，就会进行缩容。


## 常见问题

问：如果运行`kubectl get hpa`后，发现target一栏为UNKNOWN怎么办？

答：请确定HorizontalPodAutoscaler中指标名字正确。如果正确可以执行`kubectl get --raw "/apis/custom.metrics.k8s.io/v1beta1"`查看返回结果中有没有对应的指标。

问：目前GPU HPA都支持哪些指标？

答：GPU HPA支持的指标如下表。

|指标名称|说明|
|----|--|
|duty\_cycle\_current|GPU利用率|
|memory\_used\_bytes\_current|显存利用率|
|temperature\_celsius\_current|温度|
|power\_usage\_milliwatts\_current|电量|

