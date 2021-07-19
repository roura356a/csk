---
keyword: [Tensorflow模型, 推理服务]
---

# 部署Tensorflow模型的推理服务

本文介绍如何通过Arena把Tensorflow模型部署成推理服务。

-   [创建包含GPU的Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/GPU调度/使用Kubernetes默认GPU调度.md)。
-   [集群节点可以访问公网](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过公网访问集群API Server.md)。
-   [安装Arena]()。
-   您已经完成Tensorflow单机训练。具体操作，请参见[TensorFlow单机训练](/cn.zh-CN/云原生AI用户指南/模型训练/TensorFlow单机训练.md)。

    **说明：** 本文采用Tensorflow 1.15训练的Bert模型部署推理服务，并导出为saved model。


## 操作步骤

1.  执行以下命令，检查集群中可用的GPU资源。

    ```
    arena top node
    ```

    预期输出：

    ```
    NAME                      IPADDRESS      ROLE    STATUS  GPU(Total)  GPU(Allocated)
    cn-beijing.192.168.0.100  192.168.0.100  <none>  Ready   1           0
    cn-beijing.192.168.0.101  192.168.0.101  <none>  Ready   1           0
    cn-beijing.192.168.0.99   192.168.0.99   <none>  Ready   1           0
    ---------------------------------------------------------------------------------------------------
    Allocated/Total GPUs of nodes which own resource nvidia.com/gpu In Cluster:
    0/3 (0.0%)
    ```

    从上述输出可知，该集群有3个GPU节点可以用来部署模型。

2.  将准备好的模型上传到OSS上。具体操作，请参见[上传文件](/cn.zh-CN/快速入门/控制台快速入门/上传文件.md)。

3.  使用以下YAML文件，创建PV和PVC。

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
        volumeHandle: model-csi-pv   // 需要和PV名字一致。
        volumeAttributes:
          bucket: "Your Bucket"
          url: "Your oss url"
          akId: "Your Access Key Id"
          akSecret: "Your Access Key Secret"
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

4.  执行以下命令，使用`tensorflow serving`部署模型。

    ```
    arena serve tensorflow \
      --name=bert-tfserving \
      --model-name=chnsenticorp  \
      --gpus=1  \
      --image=tensorflow/serving:1.15.0-gpu \
      --data=model-pvc:/models \
      --model-path=/models/tensorflow \
      --version-policy=specific:1623831335
    ```

    预期输出：

    ```
    configmap/bert-tfserving-202106251556-tf-serving created
    configmap/bert-tfserving-202106251556-tf-serving labeled
    configmap/bert-tfserving-202106251556-tensorflow-serving-cm created
    service/bert-tfserving-202106251556-tensorflow-serving created
    deployment.apps/bert-tfserving-202106251556-tensorflow-serving created
    INFO[0003] The Job bert-tfserving has been submitted successfully
    INFO[0003] You can run `arena get bert-tfserving --type tf-serving` to check the job status
    ```

5.  执行以下命令，查看`tensorflow serving`部署情况。

    ```
    arena serve list
    ```

    预期输出：

    ```
    NAME            TYPE        VERSION       DESIRED  AVAILABLE  ADDRESS        PORTS
    bert-tfserving  Tensorflow  202106251556  1        1          172.16.95.171  GRPC:8500,RESTFUL:8501
    ```

6.  执行以下命令，查看推理服务详情。

    ```
    arena serve get bert-tfserving
    ```

    预期输出：

    ```
    Name:       bert-tfserving
    Namespace:  inference
    Type:       Tensorflow
    Version:    202106251556
    Desired:    1
    Available:  1
    Age:        4m
    Address:    172.16.95.171
    Port:       GRPC:8500,RESTFUL:8501
    
    
    Instances:
      NAME                                                             STATUS   AGE  READY  RESTARTS  NODE
      ----                                                             ------   ---  -----  --------  ----
      bert-tfserving-202106251556-tensorflow-serving-8554d58d67-jd2z9  Running  4m   1/1    0         cn-beijing.192.168.0.88
    ```

    从上述输出可知，通过`tensorflow serving`部署模型成功，并提供了8500（GRPC）和8501（HTTP）两个API端口。

7.  配置公网Ingress。具体操作，请参见[创建Ingress路由](/cn.zh-CN/Kubernetes集群用户指南/网络/Ingress管理/创建Ingress路由.md)。

    **说明：** 通过`arena serve tensorflow`部署的推理服务默认提供的是ClusterIP，不能直接访问。您需要为推理服务创建Ingress，具体参数设置如下：

    -   **命名空间**选择**inference**。
    -   服务的**端口**配置为8501端口（RESTFUL）。
8.  路由创建成功后，您可以在**路由**页面的**规则**列获取到Ingress地址。

    ![12](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0511246261/p295140.png)

9.  利用获取的Ingress地址，执行以下命令，调用推理服务接口。更多关于`tensorflow serving`的信息，请参见API文档[Tensorflow Serving API](https://github.com/tensorflow/serving/blob/master/tensorflow_serving/g3doc/api_rest.md)。

    ```
    curl "http://<Ingress地址>"
    ```

    预期输出：

    ```
    {
     "model_version_status": [
      {
       "version": "1623831335",
       "state": "AVAILABLE",
       "status": {
        "error_code": "OK",
        "error_message": ""
       }
      }
     ]
    }
    ```

    从上述输出可知您已成功调用推理服务接口，即已成功部署推理服务。


