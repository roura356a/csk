---
keyword: [GPU, 弹性伸缩, external metrics, HPA]
---

# GPU指标弹性伸缩

Kubernetes提供了External Metrics机制，您可以借助其实现GPU弹性伸缩。本文介绍如何根据GPU指标实现容器的弹性伸缩。

您已完成[创建托管GPU集群](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU管理/创建异构计算集群/创建托管GPU集群.md)或者[创建专有GPU集群](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU管理/创建异构计算集群/创建专有GPU集群.md)。

在高性能计算领域，比如深度学习模型训练、推理等场景，通常需要使用GPU来做计算加速。为了节省成本，您往往需要根据GPU指标（利用率、显存）来做弹性伸缩。虽然默认的HPA（Horizontal Pod Autoscaler）组件并不支持根据GPU指标实现弹性伸缩，但Kubernetes提供的[External Metrics机制](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/#autoscaling-on-metrics-not-related-to-kubernetes-objects)可以帮助您实现GPU弹性伸缩。

![hpa](../images/p174927.jpeg "GPU弹性伸缩原理图")

## 部署ack-prometheus-operator

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏选择**市场** \> **应用目录**，然后单击**ack-prometheus-operator**。

3.  在应用目录 - ack-prometheus-operator页面，单击**参数**页签，然后修改以下参数：

    -   设置prometheusAdpater的enabled为true。

        ```
        ## Configuration for prometheusAdpater
        prometheusAdpater:
        affinity:
        # the switch for prometheusAdpater
        enabled: true
        #
        replicas: 1
        
        AdapterConfigReloaderImage:
          repository:  registry.cn-beijing.aliyuncs.com/acs/configmap-reload
          tag: v0.0.1
          pullPolicy: IfNotPresent
         # 
        image:
          repository: quay.io/coreos/k8s-prometheus-adapter-amd64
          tag: v0.4.1
          pullPolicy: IfNotPresent
        # Specifies whether a service account should be created
        serviceAccount:
          create: true
          # The name of the service account to use.
          # If not set and create is true, a name is generated using the fullname template
          name:
        ```

    -   设置gpu\_exporter的enabled为true。

        ```
        gpu_exporter:
        enabled: true
        
        image:
          repository: registry.cn-hangzhou.aliyuncs.com/acs/gpu-prometheus-exporter
          tag: 0.1-31fa45b
        
        imagePullPolicy: IfNotPresent
        
        serviceMonitor:
          relabelings:
          metricRelabelings:
        
        nodeSelector: ""
        ```

4.  在页面右侧选择要部署的目标集群，然后单击**创建**。


## 测试GPU弹性伸缩

本文通过在GPU上部署一个模型推理服务，然后对其进行压测。根据GPU利用率（gpu\_ducy\_cycle）来测试弹性伸缩。

1.  部署推理服务。

    1.  使用以下YAML文件内容部署推理服务。

        ```
        apiVersion: extensions/v1beta1
        kind: Deployment
        metadata:
          name: bert-intent-detection
        spec:
          replicas: 1
          template:
            metadata:
              labels:
                app: bert-intent-detection
            spec:
              containers:
              - name: bert-container
                image: registry.cn-beijing.aliyuncs.com/yukong/bert-intent-detection:1.0.1
                ports:
                - containerPort: 80
                resources:
                  limits:
                    nvidia.com/gpu: 1
        ---
        apiVersion: v1
        kind: Service
        metadata:
          labels:
            run: bert-intent-detection
          name: bert-intent-detection-svc
        spec:
          ports:
          - port: 8500
            targetPort: 80
          selector:
            app: bert-intent-detection
          type: LoadBalancer
        ```

    2.  执行以下命令查看Pod和Service。

        查看Pod命令：

        ```
        kubectl get pods -o wide
        ```

        输出：

        ```
        NAME                                    READY   STATUS    RESTARTS   AGE     IP             NODE                       NOMINATED NODE   READINESS GATES
        bert-intent-detection-7b6667bd9-kgx2g   1/1     Running   0          8m58s   172.20.0.207   cn-beijing.192.168.0.114   <none>           <none>
        ```

        查看Service命令：

        ```
        kubectl get svc bert-intent-detection-svc
        ```

        输出：

        ```
        NAME                        TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)          AGE
        bert-intent-detection-svc   LoadBalancer   172.21.5.80   101.200.xx.xx 8500:32599/TCP   9m16s
        ```

    3.  通过SSH登录节点192.168.0.114后，执行以下命令查看GPU使用情况。

        ```
        nvidia-smi
        ```

        输出：

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

        输出：

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

2.  绑定HPA。

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

        输出：

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

        输出：

        ```
        NAME      REFERENCE                          TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
        gpu-hpa   Deployment/bert-intent-detection   22/20     1         10        1          13m
        ```

    2.  当TARGETS中当前GPU利用率超过20时，触发弹性伸缩，此时ACK集群开始扩容。执行以下命令查看伸缩情况。

        ```
        kubectl get pods
        ```

        输出：

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

