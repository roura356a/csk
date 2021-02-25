---
keyword: GPU共享模型推理
---

# 提交GPU共享模型推理任务

在某些场景下，您可能需要多个模型推理任务共享同一块GPU以提高GPU的利用率。本文介绍如何使用Arena提交一个GPU共享模型推理任务。

-   您已创建ACK Pro版集群，且集群的Kubernetes版本大于等于1.18.8。具体步骤，请参见[创建ACK Pro版集群](/cn.zh-CN/Kubernetes集群用户指南/ACK Pro集群/创建ACK Pro版集群.md)。
-   已安装Arena客户端，且版本大于等于0.5.0。具体操作，请参见[t1917487.dita\#task\_1917487](/cn.zh-CN/解决方案/AI解决方案/环境准备/通过组件安装最新版的Arena.md)。
-   [安装并使用共享GPU组件和资源工具](/cn.zh-CN/Kubernetes集群用户指南/ACK Pro集群/共享GPU专业版/安装并使用共享GPU组件和资源工具.md)。

## 操作步骤

1.  执行以下命令检查集群可用的GPU资源。

    ```
    arena top node
    ```

    系统输出类似以下结果：

    ```
    NAME                      IPADDRESS       ROLE    STATUS    GPU(Total)  GPU(Allocated)
    cn-beijing.192.168.1.108  192.168.20.255  <none>  Ready     0           0
    cn-beijing.192.168.8.10   192.168.8.10    <none>  Ready     0           0
    cn-beijing.192.168.1.101  192.168.1.101   <none>  Ready     1           0
    cn-beijing.192.168.1.112  192.168.1.112   <none>  Ready     1           0
    cn-beijing.192.168.8.252  192.168.8.252   <none>  Ready     1           0
    ---------------------------------------------------------------------------------------------------
    Allocated/Total GPUs In Cluster:
    0/3 (0.0%)
    ```

    从以上输出结果，可以看到集群共有3块GPU，已使用0块，使用率为0.0%。

2.  使用Arena提交推理任务。

    使用以下Yaml文件示例提交一个推理任务：

    ```
    arena serve tensorflow \
        --name=mymnist2 \
        --model-name=mnist \
        --gpumemory=3 \
        --image=registry.cn-beijing.aliyuncs.com/ai-samples/tensorflow:latest-gpu-mnist \
        --model-path=/tfmodel/mnist \
        --version-policy=specific:2
    ```

    参数解释如下表所示：

    |参数|说明|
    |--|--|
    |--name|指定任务名称。|
    |--model-name|指定模型名称。|
    |--gpumemory|指定申请显存的大小，例如：一块GPU有8GiB显存，第一个任务使用`--gpumemory=3`申请3GiB显存，那么这张卡还剩5GiB显存。第二个任务使用`--gpumemory=4`申请4GiB显存，此时这两个任务都在这块GPU上运行。|
    |--image|指定任务所需的镜像。|
    |--model-path|指定模型文件在任务容器中的存放路径。|
    |--version-policy|指定模型版本，例如：`--version-policy=specific:2`表示指定模型版本为**2**的模型生效（在--model-path指定路径下存在一个为**2**的目录）。|

    -   本次提交的是一个Tensorflow类型的推理任务。
    -   训练模型已在制作Docker镜像时添加到镜像中，如果您没有将模型文件放入镜像中，您可以创建一个PVC，然后通过`arena serve tensorflow`的--data参数将PVC挂载到任务的容器中。
3.  执行以下命令，查看所有任务。

    ```
    arena serve list
    ```

    系统输出类似以下结果：

    ```
    NAME      TYPE        VERSION       DESIRED  AVAILABLE  ADDRESS       PORTS
    mymnist1  Tensorflow  202101162119  1        0          172.16.3.123  GRPC:8500,RESTFUL:8501
    mymnist2  Tensorflow  202101191447  1        1          172.16.1.147  GRPC:8500,RESTFUL:8501
    ```

4.  执行以下命令，查看提交的任务的详细信息。

    ```
    arena serve get mymnist2
    ```

    系统输出类似以下结果：

    ```
    Name:           mymnist2
    Namespace:      default
    Type:           Tensorflow
    Version:        202101191447
    Desired:        1
    Available:      1
    Age:            20m
    Address:        172.16.1.147
    Port:           GRPC:8500,RESTFUL:8501
    GPUMemory(GiB): 3
    
    Instances:
      NAME                                                       STATUS   AGE  READY  RESTARTS  GPU(Memory/GiB)  NODE
      ----                                                       ------   ---  -----  --------  ---------------  ----
      mymnist2-202101191447-tensorflow-serving-7f64bf9749-mtnpc  Running  20m  1/1    0         3                cn-beijing.192.168.1.112
    ```

    **说明：** 当Desired数量与Available数量相等时，代表任务准备就绪。

5.  执行以下命令查看任务日志。

    ```
    arena serve logs mymnist2 -t 10
    ```

    **说明：** `-t 10`表示仅显示最后10行日志信息。

    系统输出类似以下结果：

    ```
    2021-01-18 13:21:58.482985: I external/org_tensorflow/tensorflow/cc/saved_model/loader.cc:206] Restoring SavedModel bundle.
    2021-01-18 13:21:58.483673: I external/org_tensorflow/tensorflow/core/platform/profile_utils/cpu_utils.cc:112] CPU Frequency: 2500005000 Hz
    2021-01-18 13:21:58.508734: I external/org_tensorflow/tensorflow/cc/saved_model/loader.cc:190] Running initialization op on SavedModel bundle at path: /tfmodel/mnist/2
    2021-01-18 13:21:58.513041: I external/org_tensorflow/tensorflow/cc/saved_model/loader.cc:277] SavedModel load for tags { serve }; Status: success: OK. Took 798017 microseconds.
    2021-01-18 13:21:58.513263: I tensorflow_serving/servables/tensorflow/saved_model_warmup_util.cc:59] No warmup data file found at /tfmodel/mnist/2/assets.extra/tf_serving_warmup_requests
    2021-01-18 13:21:58.513467: I tensorflow_serving/core/loader_harness.cc:87] Successfully loaded servable version {name: mnist2 version: 2}
    2021-01-18 13:21:58.516620: I tensorflow_serving/model_servers/server.cc:371] Running gRPC ModelServer at 0.0.0.0:8500 ...
    [warn] getaddrinfo: address family for nodename not supported
    2021-01-18 13:21:58.521317: I tensorflow_serving/model_servers/server.cc:391] Exporting HTTP/REST API at:localhost:8501 ...
    [evhttp_server.cc : 238] NET_LOG: Entering the event loop ...
    ```

6.  使用以下Yaml文件示例创建一个名为`tfserving-test-client`的Pod，用于验证Tensorflow推理服务是否可用。

    ```
    cat <<EOF | kubectl create -f -
    kind: Pod
    apiVersion: v1
    metadata:
      name: tfserving-test-client
    spec:
      containers:
      - name: test-client
        image: registry.cn-beijing.aliyuncs.com/ai-samples/tensorflow-serving-test-client:curl
        command: ["sleep","infinity"]
        imagePullPolicy: IfNotPresent
    EOF
    ```

7.  验证部署的Tensorflow推理服务任务是否可用。

    1.  执行以下命令，获取访问IP和端口。

        ```
        arena serve list
        ```

        系统输出类似以下结果：

        ```
        NAME      TYPE        VERSION       DESIRED  AVAILABLE  ADDRESS       PORTS
        mymnist1  Tensorflow  202101162119  1        0          172.16.3.123  GRPC:8500,RESTFUL:8501
        mymnist2  Tensorflow  202101191447  1        1          172.16.1.147  GRPC:8500,RESTFUL:8501
        ```

        从以上输出结果，可以看到`mymnist2`的IP为172.16.1.147，端口为8501。

    2.  执行以下命令验证Tensorflow推理服务是否可用。

        ```
        kubectl exec -ti tfserving-test-client bash validate.sh 172.16.1.147 8501
        ```

        系统输出类似以下结果：

        ```
        {
            "predictions": [[2.04608277e-05, 1.72721537e-09, 7.74099826e-05, 0.00364777911, 1.25222937e-06, 2.27521796e-05, 1.14668763e-08, 0.99597472, 3.68833389e-05, 0.000218785644]
            ]
        }
        ```

        从以上输出的返回数据可知：

        -   `validate.sh`脚本中包含请求的数据实际上是`mnist`测试数据集中某张图片的像素列表。
        -   模型在所有个位数（0~9）的数据中以最高概率（0.99597472）将输入数据预测为“7”。

