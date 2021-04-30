---
keyword: share GPU among inference tasks
---

# Submit an inference task to use shared GPU resources

In specific scenarios, you may want to share a GPU device among multiple inference tasks to improve GPU utilization. This topic describes how to use Arena to submit an inference task to use shared GPU resources.

-   A professional Kubernetes cluster is created and the Kubernetes version is 1.18.8 or later. For more information, see [Create a professional managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Create a professional managed Kubernetes cluster.md).
-   The Arena client is installed and the Arena version is 0.5.0 or later. For more information, see [Install the latest version of Arena](/intl.en-US/Solutions/Deep learning solution/Preparations/Install the latest version of Arena.md).
-   [Install and use ack-ai-installer and the GPU scheduling inspection tool](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/GPU Resource scheduling/cGPU Professional Edition/Install and use ack-ai-installer and the GPU scheduling inspection tool.md).

## Procedure

1.  Run the following command to query the available GPU resources in the cluster:

    ```
    arena top node
    ```

    Expected output:

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

    The preceding output shows that the cluster has three GPUs, and the utilization rate of each GPU is 0.0%.

2.  Use Arena to submit an inference task.

    In this example, the following YAML file is used to submit an inference task:

    ```
    arena serve tensorflow \
        --name=mymnist2 \
        --model-name=mnist \
        --gpumemory=3 \
        --image=registry.cn-beijing.aliyuncs.com/ai-samples/tensorflow:latest-gpu-mnist \
        --model-path=/tfmodel/mnist \
        --version-policy=specific:2
    ```

    The following table describes the parameters.

    |Parameter|Description|
    |---------|-----------|
    |--name|The name of the task.|
    |--model-name|The name of the model.|
    |--gpumemory|The requested amount of GPU memory. For example, a GPU device has 8 GiB of memory. If the first task requires 3 GiB of memory \(`--gpumemory=3`\), 5 GiB of memory remains available on the GPU device. If the second task requires 4 GiB of memory \(`--gpumemory=4`\), the two tasks can run on the same GPU device.|
    |--image|The image that is required to run the task.|
    |--model-path|The path of the models in the container.|
    |--version-policy|The version of the model that you want to use. For example, `--version-policy=specific:2` specifies that version **2** of the model is used. The model of version **2** can be found in the path that is specified by --model-path.|

    -   In this example, a TensorFlow task is submitted.
    -   The training model is already added to the Docker image. If you did not add the model to an image, you can create a persistent volume claim \(PVC\) and run the `arena serve tensorflow` command to mount the PVC on the container that runs the task. You must use the --data parameter to specify the PVC.
3.  Run the following command to list all tasks:

    ```
    arena serve list
    ```

    Expected output:

    ```
    NAME      TYPE        VERSION       DESIRED  AVAILABLE  ADDRESS       PORTS
    mymnist1  Tensorflow  202101162119  1        0          172.16.3.123  GRPC:8500,RESTFUL:8501
    mymnist2  Tensorflow  202101191447  1        1          172.16.1.147  GRPC:8500,RESTFUL:8501
    ```

4.  Run the following command to query the details of the submitted task:

    ```
    arena serve get mymnist2
    ```

    Expected output:

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

    **Note:** If the value of Desired equals the value of Available, it indicates that the task is ready.

5.  Run the following command to query the task log:

    ```
    arena serve logs mymnist2 -t 10
    ```

    **Note:** `-t 10` specifies that only the last 10 lines of the log are returned.

    Expected output:

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

6.  Use the following YAML file to create a pod named `tfserving-test-client` and check whether the TensorFlow service is available.

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

7.  Check whether the TensorFlow service is available.

    1.  Run the following command to query the IP address and port of the service:

        ```
        arena serve list
        ```

        Expected output:

        ```
        NAME      TYPE        VERSION       DESIRED  AVAILABLE  ADDRESS       PORTS
        mymnist1  Tensorflow  202101162119  1        0          172.16.3.123  GRPC:8500,RESTFUL:8501
        mymnist2  Tensorflow  202101191447  1        1          172.16.1.147  GRPC:8500,RESTFUL:8501
        ```

        The preceding output shows that the IP address of `mymnist2` is 172.16.1.147 and the port is 8501.

    2.  Run the following command to check whether the TensorFlow service is available:

        ```
        kubectl exec -ti tfserving-test-client bash validate.sh 172.16.1.147 8501
        ```

        Expected output:

        ```
        {
            "predictions": [[2.04608277e-05, 1.72721537e-09, 7.74099826e-05, 0.00364777911, 1.25222937e-06, 2.27521796e-05, 1.14668763e-08, 0.99597472, 3.68833389e-05, 0.000218785644]
            ]
        }
        ```

        The preceding output shows that:

        -   The data requested by the `validate.sh` script is the pixel values of a picture in the `mnist` data set.
        -   The model predicts that the input data is 7 among all single-digit numbers from 0 to 9, with the highest probability of 0.99597472.

