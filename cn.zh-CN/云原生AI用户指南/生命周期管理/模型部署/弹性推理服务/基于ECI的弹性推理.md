---
keyword: [ECI, 弹性推理, 工作负载]
---

# 基于ECI的弹性推理

AI任务分为模型训练和模型推理。模型推理的调用会随着业务需求而进行动态变化。在大规模高并发的节点需求情况下，常规的部署方案无法满足此类需求。阿里云提供了基于阿里云弹性容器实例ECI（Elastic Container Instance）的运行工作负载能力，满足推理服务的弹性伸缩的需求。本文介绍如何基于ECI运行弹性推理工作负载。

-   已完成模型训练。本文采用TensorFlow训练的图像识别模型。
-   您需要提前安装组件，具体操作，请参见[管理组件](/cn.zh-CN/Kubernetes集群用户指南/集群/升级集群/管理组件.md)。以下为需要安装的组件：
    -   安装seldon-core组件。
    -   安装ack-virtual-node组件。
    -   安装ack-kubernetes-elastic-workload组件。
    -   安装ack-alibaba-cloud-metrics-adapter组件。
-   已完成AI Dashboard的安装。具体操作，请参见[安装云原生AI套件](/cn.zh-CN/云原生AI用户指南/环境准备/安装云原生AI套件.md)。

## 操作步骤

1.  将训练模型上传到OSS上。具体操作，请参见[上传文件](/cn.zh-CN/快速入门/控制台快速入门/上传文件.md)。

2.  创建PV和PVC。

    1.  使用以下模板创建名为pvc.yaml的文件。

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
        -   `otherOpts`：挂载OSS时支持定制化参数输入。
        -   `akId`：账号的AccessKey ID，该账号需要拥有访问OSS的权限。
        -   `akSecret`：账号的AccessKey Secret，该账号需要拥有访问OSS的权限。
    2.  执行以下命令，创建PV和PVC。

        ```
        kubectl apply -f pvc.yaml
        ```

3.  部署推理服务。

    1.  使用以下YAML内容创建名为fashion.yaml的文件。

        ```
        apiVersion: machinelearning.seldon.io/v1
        kind: SeldonDeployment
        metadata:
          name: fashion-mnist-eci
          namespace: default
        spec:
          name: seldon
          annotations:
            workload: "seldon-4f51ae301054417c8bacf0fea6fb4321"
          replicas: 1
          predictors:
          - name: predictor
            replicas: 1
            componentSpecs:
            - spec:
                containers:
                - image: registry.cn-beijing.aliyuncs.com/deepai/seldonio-tfserving-proxy_rest:1.3.0
                  name: tfserving-proxy
                  resources:
                    requests:
                      cpu: '0.5'
                - image: tensorflow/serving:2.3.0-gpu
                  name: tfserving-gpu
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

        -   `limits: nvidia.com/gpu`：最多可使用的GPU卡。
        -   `requests: nvidia.com/gpu`：需要使用的GPU卡。
        -   `model_name`：模型的名称。
        -   `model_base_path`：模型的访问路径。
    2.  执行以下命令，部署推理服务。

        ```
        kubectl apply -f fashion.yaml
        ```

4.  部署弹性伸缩组件ElasticWorkload。

    1.  创建elasticworkload.yaml。

        ```
        apiVersion: autoscaling.alibabacloud.com/v1beta1
        kind: ElasticWorkload
        metadata:
          name: fashion-mnist-eci-ew
        spec:
          sourceTarget:
            name: fashion-mnist-eci-predictor-0-tfserving-proxy-tfserving-gpu      
            kind: Deployment
            apiVersion: apps/v1
            min: 0                                                         
            max: 1                              
          replicas: 1                        
          elasticUnit:
          - name: virtual-kubelet-1
            annotations:                                                      
              virtual-kubelet: "true"
              k8s.aliyun.com/eci-use-specs: ecs.gn6i-c4g1.xlarge
            nodeSelector:                                                
              type: "virtual-kubelet"
            tolerations:                                                 
            - key: "virtual-kubelet.io/provider"
              operator: "Exists"
            min: 1
            max: 3
          - name: virtual-kubelet-2
            annotations:                                                      
              virtual-kubelet: "true"
              k8s.aliyun.com/eci-spot-strategy: SpotAsPriceGo
              k8s.aliyun.com/eci-use-specs: ecs.gn6i-c4g1.xlarge
            nodeSelector:                                                
              type: "virtual-kubelet"
            tolerations:                                                 
            - key: "virtual-kubelet.io/provider"
              operator: "Exists"
            min: 1
            max: 10
        ```

        默认有1个Replicas，并包含两个Unit。

        -   第一个Unit为普通的ECI实例，最大Replicas数为3。
        -   第二个Unit为抢占式ECI实例，最小Replicas数为1，最大为10。
        触发扩容时，当Replicas数超过1，会先安装Unit 1指定的实例规格扩容。当Replicas数超过Unit 1的最大数量，则会安装Unit 2的实例规格进行扩容。

    2.  执行以下命令，部署弹性伸缩组件。

        ```
        kubectl apply -f elasticworkload.yaml
        ```

5.  创建HPA。

    1.  创建hpa.yaml。

        ```
        apiVersion: autoscaling/v2beta1
        kind: HorizontalPodAutoscaler
        metadata:
          name: fashion-mnist-eci-hpa
          namespace: default
        spec:
          scaleTargetRef:
            apiVersion: autoscaling.alibabacloud.com/v1beta1
            kind: ElasticWorkload
            name: fashion-mnist-eci-ew
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
                  sls.ingress.route: "default-fashion-mnist-eci-predictor-8000"
              targetAverageValue: 10
        ```

        -   `scaleTargetRef`：通过`scaleTargetRef`设置当前HPA绑定的对象，在本例中绑定是名叫fashion-mnist-eci-ew的ElasticWorkload。
        -   `minReplicas`：最小副本数。
        -   `maxReplicas`：最大副本数。
        -   `sls.project`：集群的日志项目名称
        -   `sls.logstore`：日志库的名称。
        -   `sls.ingress.route`：Ingress路由。
        -   `metricName`：QPS的指标名称。
        -   `targetAverageValue`：触发弹性扩容的QPS值。本例设置为10，表示当QPS大于10时，触发弹性扩容。
    2.  执行以下命令，创建HPA。

        ```
        kubectl apply -f hpa.yaml
        ```

6.  为推理服务创建Ingress。

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

    2.  在控制台左侧导航栏中，单击**集群**。

    3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

    4.  在集群管理页左侧导航栏中，选择**服务与路由** \> **路由**。

    5.  在路由页面单击**创建**。

    6.  在创建对话框中设置服务为步骤[3](#step_h71_3x3_zcf)的推理服务，其他参数配置请参见[Ingress基本操作](/cn.zh-CN/Kubernetes集群用户指南/网络/Ingress管理/Ingress基本操作.md)。然后单击**创建**。

7.  执行以下命令，查看Ingress。

    ```
    kubectl get ingress
    ```

    预期输出：

    ```
    NAME                CLASS    HOSTS                                                                             ADDRESS          PORTS   AGE
    fashion-mnist-eci   <none>   fashion-mnist-eci.c9522b942edaf4ec98c2cbc351ec8****.cn-beijing.alicontainer.com   101.200.XX.XX    80      19m
    ```

    您可以通过Ingress的域名访问推理服务。

8.  对推理服务进行压测，然后登录AI Dashboard。

    在登录AI Dashboard前，您需要安装和访问AI Dashboard。安装和访问AI Dashboard的具体步骤，请参见[安装云原生AI套件](/cn.zh-CN/云原生AI用户指南/环境准备/安装云原生AI套件.md)。

9.  在AI Dashboard控制台导航栏选择**弹性任务** \> **任务列表**，查看推理服务详情。

    可以看到扩容出来的Pod中，既有普通的ECI实例，也有抢占式（Spot）实例。

    ![实例](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1816772161/p240488.png)


