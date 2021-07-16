---
keyword: [ARMS Prometheus, GPU, 资源监控]
---

# 使用ARMS Prometheus监控集群GPU资源

本文介绍如果通过阿里云ARMS Prometheus对GPU资源进行监控，查看GPU各项指标。

您已完成以下操作：

-   [创建托管GPU集群](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/创建异构计算集群/创建托管GPU集群.md)或[创建专有GPU集群](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/创建异构计算集群/创建专有GPU集群.md)。
-   开通[阿里云ARMS Prometheus服务](https://arms.console.aliyun.com/#/home)。

## 安装阿里云ARMS Prometheus监控

在使用阿里云ARMS Prometheus监控对GPU进行监控前，您需要安装阿里云ARMS Prometheus监控。

您有三种安装阿里云ARMS Prometheus监控的方式。有关具体操作，请参见[阿里云ARMS Prometheus监控](/cn.zh-CN/Kubernetes集群用户指南/可观测性/监控管理/阿里云Prometheus监控.md)。

## 使用阿里云ARMS Prometheus进行GPU监控

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

3.  在集群管理页左侧导航栏中，选择**运维管理** \> **Prometheus监控**。

4.  在**Prometheus监控**大盘列表页面，单击**GPU APP**和**GPU Node**页签，您分别可以看到**GPU APP**和**GPU Node**两个监控大盘。

    -   **GPU APP**用于监控Pod的GPU使用情况。
    -   **GPU Node**用于监控集群节点的GPU使用情况。
5.  使用以下YAML文件在GPU节点上部署一个服务，测试监控效果。

    ```
    apiVersion: apps/v1
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
            image: registry.cn-beijing.aliyuncs.com/ai-samples/bert-intent-detection:1.0.1
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

6.  在**Prometheus监控**大盘列表页面，单击**GPU APP**页签。

    在**GPU APP**监控页面，您可以看到GPU显存、使用率、电量、稳定性几项指标，以及部署在GPU节点上的应用。

    ![GPU](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6797913061/p175169.jpeg)

7.  压测部署在GPU节点上的应用，查看监控状态的变化。

    1.  执行以下命令查看推理服务并获取IP地址。

        ```
        kubectl get svc bert-intent-detection-svc
        ```

        预期输出：

        ```
        NAME                        TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)          AGE
        bert-intent-detection-svc   LoadBalancer   172.23.5.253   123.56.xx.xx   8500:32451/TCP   14m
        ```

    2.  执行以下命令进行压测。

        ```
        hey -z 10m -c 100 "http://123.56.xx.xx:8500/predict?query=music"
        ```

        下图可以看出压测时，GPU利用率有了明显的变化。

        ![GPU2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6797913061/p175170.jpeg)


