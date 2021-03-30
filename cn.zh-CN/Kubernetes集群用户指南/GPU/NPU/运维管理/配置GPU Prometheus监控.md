---
keyword: [Prometheus, GPU, 资源监控]
---

# 配置GPU Prometheus监控

本文介绍如果通过阿里云Prometheus对GPU资源进行监控，查看GPU各项指标。

您已完成以下操作：

-   [创建托管GPU集群](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/创建异构计算集群/创建托管GPU集群.md)或[创建专有GPU集群](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/创建异构计算集群/创建专有GPU集群.md)。
-   开通[阿里云Prometheus服务](https://prometheus.console.aliyun.com/#/home)。

## 安装阿里云Prometheus监控

在使用阿里云Prometheus监控对GPU进行监控前，您需要安装阿里云Prometheus监控。

您有三种安装阿里云Prometheus监控的方式。有关具体操作，请参见[开启阿里云Prometheus监控](/cn.zh-CN/Kubernetes集群用户指南/可观测性/监控管理/阿里云Prometheus监控.md)。

## 使用阿里云Prometheus进行GPU监控

1.  登录[阿里云Prometheus服务控制台](https://prometheus.console.aliyun.com/#/home)。

2.  在监控列表页面单击目标集群的名称。

    在大盘列表页面可以看到目标ACK集群的监控项。

3.  使用以下YAML文件在GPU节点上部署一个服务，测试监控效果。

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

4.  在[阿里云Prometheus服务控制台](https://prometheus.console.aliyun.com/#/home)，在监控列表页面单击目标集群的名称。在大盘列表页面单击GPU，然后单击**GPU APP**。

    在GPU监控页面，可以看到GPU显存、使用率、电量、稳定性几项指标，以及部署在GPU节点上的应用。

    ![GPU](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6797913061/p175169.jpeg)

5.  压测部署在GPU节点上的应用，查看监控状态的变化。

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


