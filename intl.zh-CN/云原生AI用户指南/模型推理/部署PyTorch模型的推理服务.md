---
keyword: [PyTorch模型, 推理服务]
---

# 部署PyTorch模型的推理服务

PyTorch是一种深度学习计算框架，可用来训练模型。本文介绍如何通过Triton或TorchServe方式部署PyTorch模型的推理服务。

-   已创建包含GPU的Kubernetes集群。具体操作，请参见[创建包含GPU的Kubernetes集群](/intl.zh-CN/Kubernetes集群用户指南/GPU/NPU/GPU调度/使用Kubernetes默认GPU调度.md)。
-   Kubernetes集群可以访问公网。具体操作，请参见[集群节点可以访问公网](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/集群访问控制/控制集群API Server的公网访问能力.md)。
-   已安装Arena组件。具体操作，请参见[安装Arena]()。

## 部署方式

支持通过Triton或TorchServe方式部署PyTorch模型的推理服务，推荐使用Triton方式。

## 方式一：使用Triton部署PyTorch模型的推理服务

1.  完成PyTorch单机训练，且把PyTorch模型转换成Torchscript，具体操作，请参见[PyTorch单机训练](/intl.zh-CN/云原生AI用户指南/模型训练/PyTorch单机训练.md)。

    **说明：** 本方式采用的是PyTorch 1.16训练的Bert模型，并把模型转换成Torchscript，存储在PVC的triton目录下，然后通过`nvidia triton server`来部署模型。

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

2.  执行以下命令，检查集群中可用的GPU资源。

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

3.  将准备好的模型上传到OSS上。具体操作，请参见[上传文件](/intl.zh-CN/快速入门/控制台快速入门/上传文件.md)。

4.  使用以下YAML文件，创建PV和PVC。

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

5.  执行以下命令，使用`nvidia triton server`部署模型。

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

6.  执行以下命令，查看PyTorch模型的部署情况。

    ```
    arena serve list -n inference
    ```

    预期输出：

    ```
    NAME            TYPE        VERSION       DESIRED  AVAILABLE  ADDRESS        PORTS
    bert-triton     Triton      202106251740  1        1          172.16.70.14   RESTFUL:8000,GRPC:8001
    ```

7.  执行以下命令，查看推理服务详情。

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

    从上述输出可知，通过`nvidia triton server`部署模型成功，并提供了8001（GRPC）和8000（RESTFUL）两个API端口。

8.  配置公网Ingress。具体操作，请参见[创建Ingress路由](/intl.zh-CN/Kubernetes集群用户指南/网络/Ingress管理/创建Ingress路由.md)。

    **说明：** 通过`nvidia triton server`部署的推理服务默认提供的是ClusterIP，不能直接访问。您需要为推理服务创建Ingress，具体参数设置如下：

    -   **命名空间**选择**inference**。
    -   服务的**端口**配置为8501端口（RESTFUL）。
9.  路由创建成功后，您可以在**路由**页面的**规则**列获取到Ingress地址。

    ![23](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/5671666261/p295171.png)

10. 利用获取的Ingress地址，执行以下命令，调用推理服务接口。`nvidia triton server`遵循KFServing接口规范。更多信息，请参见API文档[Nvidia Triton Server API](https://github.com/triton-inference-server/server/blob/main/docs/inference_protocols.md)。

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


## 方式二：使用TorchServe部署PyTorch模型的推理服务

1.  使用torch-model-achiver将PyTorch模型打包为TorchServe需要的.mar格式。更多信息，请参见[torch-model-achiver](https://github.com/pytorch/serve#store-a-model)。

2.  将准备好的模型上传到OSS上。具体操作，请参见[上传文件](/intl.zh-CN/快速入门/控制台快速入门/上传文件.md)。

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

4.  执行以下命令部署PyTorch模型。

    ```
    arena serve custom \
      --name=torchserve-demo \
      --gpus=1 \
      --replicas=1 \
      --image=pytorch/torchserve:0.4.2-gpu \
      --port=8000 \
      --restful-port=8001 \
      --metrics-port=8002 \
      --data=model-pvc:/data \
      'torchserve --start --model-store /data/models --ts-config /data/config/ts.properties'
    ```

    **说明：**

    -   image可以是官方镜像，也可以是自定义的TorchServe镜像。
    -   torchserve参数中`--model-store`的路径需要和您的PyTorch模型的实际路径一致。
    预期输出：

    ```
    service/torchserve-demo-202109101624 created
    deployment.apps/torchserve-demo-202109101624-custom-serving created
    INFO[0001] The Job torchserve-demo has been submitted successfully
    INFO[0001] You can run `arena get torchserve-demo --type custom-serving` to check the job status
    ```

5.  执行[方式一：使用Triton部署PyTorch模型的推理服务](#section_053_1ll_us3)的[步骤6](#step_2lb_q61_pkq)至[步骤10](#step_dyd_ktj_5h6)验证推理服务是否成功部署。


