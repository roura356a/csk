---
keyword: [ECS, 弹性推理]
---

# 基于ECS运行弹性推理工作负载

模型训练完成后，通常会被部署成推理服务。推理服务的调用量会随着业务需求动态变化，这就需要服务器能弹性扩缩容来节省成本。在大规模高并发的节点需求情况下，常规的部署方案无法满足此类需求。阿里云容器服务提供了弹性节点池，可以基于弹性节点池部署模型推理服务，满足弹性伸缩的需求。本文介绍如何基于ECS运行弹性推理工作负载。

-   已完成模型训练。本文采用TensorFlow 1.15训练的Bert模型。
-   已安装ack-alibaba-cloud-metrics-adapter组件。具体操作，请参见[管理组件](/intl.zh-CN/Kubernetes集群用户指南/组件/管理组件.md)。
-   已完成运维控制台安装。具体操作，请参见[安装云原生AI套件](/intl.zh-CN/云原生AI用户指南/环境准备/安装云原生AI套件.md)。
-   [安装Arena]()。

## 操作步骤

1.  创建弹性节点池。

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

    2.  在控制台左侧导航栏中，单击**集群**。

    3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

    4.  在集群管理页左侧导航栏中，选择**节点管理** \> **节点池**。

    5.  在**节点池**页面，单击**创建节点池**。

    6.  在**创建节点池**对话框中配置参数，然后单击**确认配置**。以下为重点参数配置，其他参数，请参见[创建ACK Pro版集群](/intl.zh-CN/Kubernetes集群用户指南/ACK Pro集群/创建ACK Pro版集群.md)。

        |参数|说明|
        |--|--|
        |自动伸缩|选中**开启自动伸缩**。|
        |付费类型|选择抢占式实例。|
        |节点标签|设置节点标签的**键**为inference，节点标签的**值**为tensorflow。|
        |多可用区扩缩容策略|选择**成本优化策略**，设置**按量实例所占实例**为30%，并开启**允许按量实例补偿**。|

2.  将训练模型上传到OSS上。具体操作，请参见[上传文件](/intl.zh-CN/快速入门/控制台快速入门/上传文件.md)。

3.  创建PV和PVC。

    1.  创建pvc.yaml。

        ```
        apiVersion: v1
        kind: PersistentVolume
        metadata:
          name: model-csi-pv
        spec:
          capacity:
            storage: 5Gi
          accessModes:
            - ReadWriteMany
          persistentVolumeReclaimPolicy: Retain
          csi:
            driver: ossplugin.csi.alibabacloud.com
            volumeHandle: model-csi-pv // 需要和PV名字一致。
            volumeAttributes:
              bucket: "<Your Bucket>"
              url: "<Your oss url>"
              akId: "<Your Access Key Id>"
              akSecret: "<Your Access Key Secret>"
              otherOpts: "-o max_stat_cache_size=0 -o allow_other"
        ---
        apiVersion: v1
        kind: PersistentVolumeClaim
        metadata:
          name: model-pvc
        spec:
          accessModes:
          - ReadWriteMany
          resources:
            requests:
              storage: 5Gi
        ```

        -   `bucket`：设置为您OSS的Bucket。
        -   `url`：OSS的访问路径。
        -   `akId`：账号的AccessKey ID，该账号需要拥有访问OSS的权限。
        -   `akSecret`：账号的AccessKey Secret，该账号需要拥有访问OSS的权限。
    2.  执行以下命令，创建PV和PVC。

        ```
        kubectl apply -f pvc.yaml
        ```

4.  执行以下命令，部署推理服务。

    ```
    arena serve tensorflow \
      --name=bert-tfserving \
      --model-name=chnsenticorp  \
      --selector=inference=tensorflow \
      --gpus=1  \
      --image=tensorflow/serving:1.15.0-gpu \
      --data=model-pvc:/models \
      --model-path=/models/tensorflow \
      --version-policy=specific:1623831335
    ```

    **说明：** 您需要通过--selector参数设置，把模型部署到步骤1中创建的节点池中。

    推理服务参数说明如下：

    -   `nodeSelector`：设置为弹性节点池的节点标签，推理服务通过nodeSelector把Pod调度到弹性节点池。本例设置为`seldon：fashion-mnist`。
    -   `limits: nvidia.com/gpu`：最多可使用的GPU卡。
    -   `requests: nvidia.com/gpu`：需要使用的GPU卡。
    -   `model_name`：模型的名称。
    -   `model_base_path`：模型的访问路径。
5.  创建HPA。

    1.  创建hpa.yaml。

        ```
        apiVersion: autoscaling/v2beta1
        kind: HorizontalPodAutoscaler
        metadata:
          name: bert-tfserving-hpa
        spec:
          scaleTargetRef:
            apiVersion: apps/v1
            kind: Deployment
            name: bert-tfserving-202107141745-tensorflow-serving
          minReplicas: 1
          maxReplicas: 10
          metrics:
          - type: External
            external:
              metricName: sls_ingress_qps
              metricSelector:
                matchLabels:
                  sls.project: "k8s-log-c210fbedb96674b9eaf15f2dc47d169a8"
                  sls.logstore: "nginx-ingress"
                  sls.ingress.route: "default-bert-tfserving-202107141745-tensorflow-serving-8501"
              targetAverageValue: 10
        ```

        HPA参数说明如下：

        -   -   `scaleTargetRef`：当前HPA绑定的对象。
-   `minReplicas`：最小副本数。
-   `maxReplicas`：最大副本数。
-   `sls.project`：集群的日志项目名称，配置规则为`k8s-log-{cluster id}`。
-   `sls.logstore`：日志库的名称，配置规则为`{namespace}-{service name}-{service port}`。
-   `sls.ingress.route`：Ingress路由。
-   `metricName`：QPS的指标名称。
-   `targetAverageValue`：触发弹性扩容的QPS值。本例设置为10，表示当QPS大于10时，触发弹性扩容。
    2.  执行以下命令，部署HPA。

        ```
        kubectl apply -f hpa.yaml
        ```

6.  配置公网Ingress。具体操作，请参见[创建Ingress路由](/intl.zh-CN/Kubernetes集群用户指南/网络/Ingress管理/创建Ingress路由.md)。

    **说明：** 通过`arena serve tensorflow`部署的推理服务默认提供的是ClusterIP，不能直接访问。您需要为推理服务创建Ingress，具体参数设置如下：

    -   **命名空间**选择**inference**。
    -   服务的**端口**配置为8501端口（RESTFUL）。
7.  路由创建成功后，您可以在**路由**页面的**规则**列获取到Ingress地址。

    ![12](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0511246261/p295140.png)

8.  对推理服务进行压测，然后登录运维控制台。

    在登录运维控制台前，您需要安装和配置访问方式，具体步骤，请参见[安装云原生AI套件](/intl.zh-CN/云原生AI用户指南/环境准备/安装云原生AI套件.md)。

9.  在运维控制台导航栏选择**弹性任务** \> **任务列表**，查看推理服务详情。

    可以看到扩容出来的Pod中，都运行在ECS实例上。其中既有按量付费实例，也有抢占式实例（Spot），且数量比例等于创建节点池时配置的比例。

    ![ESS](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1511246261/p240489.png)


