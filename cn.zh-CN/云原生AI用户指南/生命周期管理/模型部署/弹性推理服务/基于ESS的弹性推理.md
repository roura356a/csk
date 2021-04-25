---
keyword: [ESS, 弹性推理, 工作负载]
---

# 基于ESS的弹性推理

AI任务分为模型训练和模型推理。模型推理的调用会随着业务需求而进行动态变化。在大规模高并发的节点需求情况下，常规的部署方案无法满足此类需求。阿里云提供了基于弹性伸缩（Auto Scaling，原名ESS）的运行工作负载能力，满足推理服务弹性伸缩的需求。本文介绍如何基于ESS运行弹性推理工作负载。

-   已完成模型训练。本文采用TensorFlow训练的图像识别模型。
-   已安装ack-alibaba-cloud-metrics-adapter组件。具体操作，请参见[管理组件](/cn.zh-CN/Kubernetes集群用户指南/集群/升级集群/管理组件.md)。
-   已完成AI Dashboard的安装。具体操作，请参见[安装云原生AI套件](/cn.zh-CN/云原生AI用户指南/环境准备/安装云原生AI套件.md)。

## 操作步骤

1.  创建弹性节点池。

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

    2.  在控制台左侧导航栏中，单击**集群**。

    3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

    4.  在集群管理页左侧导航栏中，选择**节点管理** \> **节点池**。

    5.  在**创建节点池**对话框中配置参数，然后单击**确认配置**。以下为重点参数配置，其他参数，请参见[创建ACK Pro版集群](/cn.zh-CN/Kubernetes集群用户指南/ACK Pro集群/创建ACK Pro版集群.md)。

        |参数|说明|
        |--|--|
        |自动伸缩|选中**开启自动伸缩**。|
        |实例规格|选择抢占式实例。|
        |节点标签|设置节点标签的**键**为seldon，节点标签的**值**为fashion-mnist。|
        |多可用区扩缩容策略|选择**成本优化策略**，设置**按量实例所占实例**为30%，并开启**允许按量实例补偿**。|

2.  将训练模型上传到OSS上。具体操作，请参见[上传文件](/cn.zh-CN/快速入门/控制台快速入门/上传文件.md)。

3.  创建PV和PVC。

    1.  创建pvc.yaml。

        ```
        apiVersion: v1
        kind: PersistentVolume
        metadata:
          name: oss-csi-pv
        spec:
          capacity:
            storage: 5Gi
          accessModes:
            - ReadWriteMany
          persistentVolumeReclaimPolicy: Retain
          csi:
            driver: ossplugin.csi.alibabacloud.com
            volumeHandle: oss-csi-pv // 需要和pv名字一致；
            volumeAttributes:
              bucket: "Your Bucket"
              url: "oss-cn-beijing.aliyuncs.com"
              otherOpts: "-o max_stat_cache_size=0 -o allow_other"
              akId: "Your Access Key Id"
              akSecret: "Your Access Key Secret"
        ---
        apiVersion: v1
        kind: PersistentVolumeClaim
        metadata:
          name: oss-pvc
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

4.  部署推理服务和HPA。

    1.  创建fashion.yaml。

        ```
        apiVersion: machinelearning.seldon.io/v1
        kind: SeldonDeployment
        metadata:
          name: fashion-mnist-ecs
          namespace: default
        spec:
          name: seldon
          replicas: 1
          annotations:
            workload: "seldon-94a0874115534be9acc71dd995d8e18d"
          predictors:
          - name: predictor
            replicas: 1
            componentSpecs:
            - hpaSpec:
                minReplicas: 1
                maxReplicas: 10
                metrics:
                - type: External
                  external:
                    metricName: sls_ingress_qps
                    metricSelector:
                      matchLabels:
                        sls.project: "k8s-log-c9522b942edaf4ec98c2cbc351ec8ade6"
                        sls.logstore: "nginx-ingress"
                        sls.ingress.route: "default-fashion-mnist-ecs-predictor-8000"
                    targetAverageValue: 10 
              spec:
                containers:
                - image: registry.cn-beijing.aliyuncs.com/deepai/seldonio-tfserving-proxy_rest:1.3.0
                  name: tfserving-proxy
                  env:
                    - name: SELDON_LOG_LEVEL
                      value: DEBUG
                  resources:
                    requests:
                      cpu: '0.5'
                - image:   tensorflow/serving:2.3.0-gpu
                  name: fashion-mnist
                  args:
                  - "/usr/bin/tensorflow_model_server"
                  - "--port=7001"
                  - "--model_name=fashion-mnist"
                  - "--model_base_path=/mnt/arena/models/fashion-mnist"
                  volumeMounts:
                  - name: model-storage
                    mountPath: /mnt
                  ports:
                  - containerPort: 7001
                    protocol: TCP
                  resources:
                    limits:
                      nvidia.com/gpu: 1
                    requests:
                      nvidia.com/gpu: 1
                  securityContext:
                    runAsUser: 1000
                nodeSelector:
                  seldon: "fashion-mnist"
                volumes:
                - name: model-storage
                  persistentVolumeClaim:
                    claimName: oss-pvc
                terminationGracePeriodSeconds: 1
            graph:
              name: tfserving-proxy
              endpoint:
                type: REST
              type: MODEL
              children: []
              parameters:
              - name: rest_endpoint
                type: STRING
                value: "http://127.x.x.x:8501"
              - name: model_name
                type: STRING
                value: fashion-mnist
              - name: signature_name
                type: STRING
                value: serving_default
        ```

        -   HPA参数说明。
            -   `minReplicas`：最小副本数。
            -   `maxReplicas`：最大副本数。
            -   `sls.project`：集群的日志项目名称
            -   `sls.logstore`：日志库的名称。
            -   `sls.ingress.route`：Ingress路由。
            -   `metricName`：QPS的指标名称。
            -   `targetAverageValue`：触发弹性扩容的QPS值。本例设置为10，表示当QPS大于10时，触发弹性扩容。
        -   推理服务参数说明。
            -   `nodeSelector`：设置为弹性节点池的节点标签，推理服务通过nodeSelector把Pod调度到弹性节点池。本例设置为`seldon：fashion-mnist`。
            -   `limits: nvidia.com/gpu`：最多可使用的GPU卡。
            -   `requests: nvidia.com/gpu`：需要使用的GPU卡。
            -   `model_name`：模型的名称。
            -   `model_base_path`：模型的访问路径。
    2.  执行以下命令，部署推理服务和HPA。

        ```
        kubectl apply -f fashion.yaml
        ```

5.  为推理服务创建Ingress。

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

    2.  在控制台左侧导航栏中，单击**集群**。

    3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

    4.  在集群管理页左侧导航栏中，选择**服务与路由** \> **路由**。

    5.  在路由页面单击**创建**。

    6.  在创建对话框中设置服务为步骤[4](#step_xqi_62m_4su)的推理服务，其他参数配置请参见[Ingress基本操作](/cn.zh-CN/Kubernetes集群用户指南/网络/Ingress管理/Ingress基本操作.md)。然后单击**创建**。

6.  执行以下命令，查看Ingress。

    ```
    kubectl get ingress
    ```

    预期输出：

    ```
    NAME                CLASS    HOSTS                                                                             ADDRESS          PORTS   AGE
    fashion-mnist-ecs   <none>   fashion-mnist-ecs.c9522b942edaf4ec98c2cbc351ec8****.cn-beijing.alicontainer.com   101.200.xx.xxx   80      2m58
    ```

    您可以通过Ingress的域名访问推理服务。

7.  对推理服务进行压测，然后登录AI Dashboard。

    在登录AI Dashboard前，您需要安装和访问AI Dashboard。关于如何安装和访问AI Dashboard的具体步骤，请参见[安装云原生AI套件](/cn.zh-CN/云原生AI用户指南/环境准备/安装云原生AI套件.md)。

8.  在AI Dashboard控制台导航栏选择**弹性任务** \> **任务列表**，查看推理服务详情。

    可以看到扩容出来的Pod中，都运行在ECS实例上。其中既有按量付费实例，也有抢占式实例（Spot），且数量比例等于创建节点池时配置的比例。

    ![ESS](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8356772161/p240489.png)


