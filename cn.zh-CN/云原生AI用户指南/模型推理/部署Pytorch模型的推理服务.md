---
keyword: [Pytorch模型, 推理服务]
---

# 部署Pytorch模型的推理服务

本文介绍如何通过Arena把Pytorch模型部署成推理服务。

-   [创建包含GPU的Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/GPU调度/使用Kubernetes默认GPU调度.md)。
-   [集群节点可以访问公网](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过公网访问集群API Server.md)。
-   [安装Arena]()。
-   您已经完成Pytorch单机训练。把Pytorch模型转换成Torchscript，具体操作，请参见[TensorFlow单机训练](/cn.zh-CN/云原生AI用户指南/模型训练/TensorFlow单机训练.md)。

    **说明：** 本文采用的是Pytorch 1.16训练的Bert模型，并把模型转换成Torchscript，存储在PVC的triton目录下，然后通过`nvidia triton server`来部署模型。

    其中triton要求的模型目录结构如下：

    ```
    └── chnsenticorp # 模型名称。
        ├── 1623831335 # 模型版本。
        │   └── model.savedmodel # 模型文件。
        │       ├── saved_model.pb
        │       └── variables
        │           ├── variables.data-00000-of-00001
        │           └── variables.index
        └── config.pbtxt # triton 配置。
    ```


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
    arena serve triton \
     --name=bert-triton \
     --namespace=inference \
     --gpus=1 \
     --replicas=1 \
     --image=nvcr.io/nvidia/tritonserver:20.12-py3 \
     --data=model-pvc:/models \
     --model-repository=/models/triton
    ```

    预期输出：

    ```
    configmap/bert-triton-202106251740-triton-serving created
    configmap/bert-triton-202106251740-triton-serving labeled
    service/bert-triton-202106251740-tritoninferenceserver created
    deployment.apps/bert-triton-202106251740-tritoninferenceserver created
    INFO[0001] The Job bert-triton has been submitted successfully
    INFO[0001] You can run `arena get bert-triton --type triton-serving` to check the job status
    ```

5.  执行以下命令，查看`tensorflow serving`部署情况。

    ```
    arena serve list -n inference
    ```

    预期输出：

    ```
    NAME            TYPE        VERSION       DESIRED  AVAILABLE  ADDRESS        PORTS
    bert-triton     Triton      202106251740  1        1          172.16.70.14   RESTFUL:8000,GRPC:8001
    ```

6.  执行以下命令，查看推理服务详情。

    ```
    arena serve get bert-tfserving -n inference
    ```

    预期输出：

    ```
    Name:       bert-triton
    Namespace:  inference
    Type:       Triton
    Version:    202106251740
    Desired:    1
    Available:  1
    Age:        5m
    Address:    172.16.70.14
    Port:       RESTFUL:8000,GRPC:8001
    
    
    Instances:
      NAME                                                             STATUS   AGE  READY  RESTARTS  NODE
      ----                                                             ------   ---  -----  --------  ----
      bert-triton-202106251740-tritoninferenceserver-667cf4c74c-s6nst  Running  5m   1/1    0         cn-beijing.192.168.0.89
    ```

    从上述输出可知，通过`tensorflow serving`部署模型成功，并提供了8001（GRPC）和8000（RESTFUL）两个API端口。

7.  配置公网Ingress。具体操作，请参见[创建Ingress路由](/cn.zh-CN/Kubernetes集群用户指南/网络/Ingress管理/创建Ingress路由.md)。

    **说明：** 通过`arena serve tensorflow`部署的推理服务默认提供的是ClusterIP，不能直接访问。您需要为推理服务创建Ingress，具体参数设置如下：

    -   **命名空间**选择**inference**。
    -   服务的**端口**配置为8501端口（RESTFUL）。
8.  路由创建成功后，您可以在**路由**页面的**规则**列获取到Ingress地址。

    ![23](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5671666261/p295171.png)

9.  利用获取的Ingress地址，执行以下命令，调用推理服务接口。`nvidia triton server`遵循KFServing接口规范。更多信息，请参见API文档[Nvidia Triton Server API](https://github.com/triton-inference-server/server/blob/main/docs/inference_protocols.md)。

    ```
    curl "http://<Ingress地址>"
    ```

    预期输出：

    ```
    {
        "name":"chnsenticorp",
        "versions":[
            "1623831335"
        ],
        "platform":"tensorflow_savedmodel",
        "inputs":[
            {
                "name":"input_ids",
                "datatype":"INT64",
                "shape":[
                    -1,
                    128
                ]
            }
        ],
        "outputs":[
            {
                "name":"probabilities",
                "datatype":"FP32",
                "shape":[
                    -1,
                    2
                ]
            }
        ]
    }
    ```

    从上述输出可知您已成功调用推理服务接口，即已成功部署推理服务。


