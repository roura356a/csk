---
keyword: [GPU topology-aware scheduling, Kubernetes scheduling, deep learning, TensorFlow distributed jobs]
---

# Use GPU topology-aware scheduling to achieve optimal GPU acceleration for TensorFlow distributed jobs

Based on the scheduling framework, Container Service for Kubernetes \(ACK\) supports GPU topology-aware scheduling. This feature selects a combination of GPUs from GPU-accelerated nodes to achieve optimal GPU acceleration for training jobs. This topic describes how to use GPU topology-aware scheduling to achieve optimal GPU acceleration for TensorFlow distributed jobs.

-   [Create a professional managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Create a professional managed Kubernetes cluster.md).
-   [Arena](https://github.com/kubeflow/arena) is installed.
-   [Install the ack-ai-installer component](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/GPU Resource scheduling/GPU topology-aware scheduling/Install the ack-ai-installer component.md).
-   The following table lists the required components and versions.

    |Component|Required version|
    |---------|----------------|
    |Kubernetes|V1.18.8 and later|
    |Helm|V3.0 and later|
    |Nvidia|V418.87.01 and later|
    |NVIDIA Collective Communications Library \(NCCL\)|2.7+|
    |Docker|19.03.5|
    |Operating system|CentOS 7.6, CentOS 7.7, Ubuntu 16.04, Ubuntu 18.04, and Alibaba Cloud Linux 2|
    |Graphics card|V100|


## Limits

-   GPU topology-aware scheduling is applicable to only Message Passing Interface \(MPI\) jobs that use distributed training.
-   The resources that are requested by pods must meet specific requirements before the pods can be created to submit and start jobs. Otherwise, the requests remain pending for resources.

## Procedure

**Configure nodes**

Run the following command to set the node label and explicitly enable GPU topology-aware scheduling for nodes:

```
kubectl label node <Your Node Name\> ack.node.gpu.schedule=topology
```

**Note:** After GPU topology-aware scheduling is enabled on nodes, common GPU scheduling is no longer supported. You can run the following command to change the label and resume common GPU scheduling.

```
kubectl label node <Your Node Name\> ack.node.gpu.schedule=default --overwrite
```

**Submit a job**

Submit an MPI job and set --gputopology to `true`.

```
arena submit mpi --gputopology=true ***
```

## Example 1: Train VGG16

**Note:** In this topic, two V100 servers are deployed in the test cluster. Each V100 server has 8 GPUs.

**Use GPU topology-aware scheduling to train VGG16**

1.  Run the following command to submit a job to the cluster:

    ```
    arena submit mpi \
      --name=tensorflow-topo-4-vgg16 \
      --gpus=1 \
      --workers=4 \
      --gputopology=true \
      --image=registry.cn-hangzhou.aliyuncs.com/kubernetes-image-hub/tensorflow-benchmark:tf2.3.0-py3.7-cuda10.1 \
      "mpirun --allow-run-as-root -np "4" -bind-to none -map-by slot -x NCCL_DEBUG=INFO -x NCCL_SOCKET_IFNAME=eth0 -x LD_LIBRARY_PATH -x PATH --mca pml ob1 --mca btl_tcp_if_include eth0 --mca oob_tcp_if_include eth0 --mca orte_keep_fqdn_hostnames t --mca btl ^openib python /tensorflow/benchmarks/scripts/tf_cnn_benchmarks/tf_cnn_benchmarks.py --model=vgg16 --batch_size=64 --variable_update=horovod"
    ```

2.  Run the following command to query the state of the job:

    ```
    arena get tensorflow-topo-4-vgg16 --type mpijob
    ```

    Expected output:

    ```
    Name:      tensorflow-topo-4-vgg16
    Status:    RUNNINGNamespace: default
    Priority:  N/A
    Trainer:   MPIJOB
    Duration:  2m
    
    Instances:
      NAME                                    STATUS   AGE  IS_CHIEF  GPU(Requested)  NODE
      ----                                    ------   ---  --------  --------------  ----
      tensorflow-topo-4-vgg16-launcher-lmhjl  Running  2m   true      0               cn-shanghai.192.168.16.172
      tensorflow-topo-4-vgg16-worker-0        Running  2m   false     1               cn-shanghai.192.168.16.173
      tensorflow-topo-4-vgg16-worker-1        Running  2m   false     1               cn-shanghai.192.168.16.173
      tensorflow-topo-4-vgg16-worker-2        Running  2m   false     1               cn-shanghai.192.168.16.173
      tensorflow-topo-4-vgg16-worker-3        Running  2m   false     1               cn-shanghai.192.168.16.173
    ```

3.  Run the following command to print the job log:

    ```
    arena logs -f tensorflow-topo-4-vgg16
    ```

    Expected output:

    ```
    total images/sec: 991.92
    ```


**Use common GPU scheduling to train VGG16**

1.  Run the following command to submit a job to the cluster:

    ```
    arena submit mpi \
      --name=tensorflow-4-vgg16 \
      --gpus=1 \
      --workers=4 \
      --image=registry.cn-hangzhou.aliyuncs.com/kubernetes-image-hub/tensorflow-benchmark:tf2.3.0-py3.7-cuda10.1 \
      "mpirun --allow-run-as-root -np "4" -bind-to none -map-by slot -x NCCL_DEBUG=INFO -x NCCL_SOCKET_IFNAME=eth0 -x LD_LIBRARY_PATH -x PATH --mca pml ob1 --mca btl_tcp_if_include eth0 --mca oob_tcp_if_include eth0 --mca orte_keep_fqdn_hostnames t --mca btl ^openib python /tensorflow/benchmarks/scripts/tf_cnn_benchmarks/tf_cnn_benchmarks.py --model=vgg16 --batch_size=64 --variable_update=horovod"
    ```

2.  Run the following command to query the state of the job:

    ```
    arena get tensorflow-4-vgg16 --type mpijob
    ```

    Expected output:

    ```
    Name:      tensorflow-4-vgg16
    Status:    RUNNING
    Namespace: default
    Priority:  N/A
    Trainer:   MPIJOB
    Duration:  9s
    
    Instances:
      NAME                               STATUS   AGE  IS_CHIEF  GPU(Requested)  NODE
      ----                               ------   ---  --------  --------------  ----
      tensorflow-4-vgg16-launcher-xc28k  Running  9s   true      0               cn-shanghai.192.168.16.172
      tensorflow-4-vgg16-worker-0        Running  9s   false     1               cn-shanghai.192.168.16.172
      tensorflow-4-vgg16-worker-1        Running  9s   false     1               cn-shanghai.192.168.16.173
      tensorflow-4-vgg16-worker-2        Running  9s   false     1               cn-shanghai.192.168.16.172
      tensorflow-4-vgg16-worker-3        Running  9s   false     1               cn-shanghai.192.168.16.173
    ```

3.  Run the following command to print the job log:

    ```
    arena logs -f tensorflow-4-vgg16
    ```

    Expected output:

    ```
    total images/sec: 200.47
    ```


## Example 2: Train ResNet50

**Use GPU topology-aware scheduling to train ResNet50**

1.  Run the following command to submit a job to the cluster:

    ```
    arena submit mpi \
      --name=tensorflow-topo-4-resnet50 \
      --gpus=1 \
      --workers=4 \
      --gputopology=true \
      --image=registry.cn-hangzhou.aliyuncs.com/kubernetes-image-hub/tensorflow-benchmark:tf2.3.0-py3.7-cuda10.1 \
      "mpirun --allow-run-as-root -np "4" -bind-to none -map-by slot -x NCCL_DEBUG=INFO -x NCCL_SOCKET_IFNAME=eth0 -x LD_LIBRARY_PATH -x PATH --mca pml ob1 --mca btl_tcp_if_include eth0 --mca oob_tcp_if_include eth0 --mca orte_keep_fqdn_hostnames t --mca btl ^openib python /tensorflow/benchmarks/scripts/tf_cnn_benchmarks/tf_cnn_benchmarks.py --model=resnet50 --batch_size=64  --variable_update=horovod"
    ```

2.  Run the following command to query the state of the job:

    ```
    arena get tensorflow-topo-4-resnet50 --type mpijob
    ```

    Expected output:

    ```
    Name:      tensorflow-topo-4-resnet50
    Status:    RUNNING
    Namespace: default
    Priority:  N/A
    Trainer:   MPIJOB
    Duration:  8s
    
    Instances:
      NAME                                       STATUS   AGE  IS_CHIEF  GPU(Requested)  NODE
      ----                                       ------   ---  --------  --------------  ----
      tensorflow-topo-4-resnet50-launcher-7ln8j  Running  8s   true      0               cn-shanghai.192.168.16.172
      tensorflow-topo-4-resnet50-worker-0        Running  8s   false     1               cn-shanghai.192.168.16.173
      tensorflow-topo-4-resnet50-worker-1        Running  8s   false     1               cn-shanghai.192.168.16.173
      tensorflow-topo-4-resnet50-worker-2        Running  8s   false     1               cn-shanghai.192.168.16.173
      tensorflow-topo-4-resnet50-worker-3        Running  8s   false     1               cn-shanghai.192.168.16.173
    ```

3.  Run the following command to print the job log:

    ```
    arena logs -f tensorflow-topo-4-resnet50
    ```

    Expected output:

    ```
    total images/sec: 1471.55
    ```


**Use common GPU scheduling to train ResNet50**

1.  Run the following command to submit a job to the cluster:

    ```
    arena submit mpi \
      --name=tensorflow-4-resnet50 \
      --gpus=1 \
      --workers=4 \
      --image=registry.cn-hangzhou.aliyuncs.com/kubernetes-image-hub/tensorflow-benchmark:tf2.3.0-py3.7-cuda10.1 \
      "mpirun --allow-run-as-root -np "4" -bind-to none -map-by slot -x NCCL_DEBUG=INFO -x NCCL_SOCKET_IFNAME=eth0 -x LD_LIBRARY_PATH -x PATH --mca pml ob1 --mca btl_tcp_if_include eth0 --mca oob_tcp_if_include eth0 --mca orte_keep_fqdn_hostnames t --mca btl ^openib python /tensorflow/benchmarks/scripts/tf_cnn_benchmarks/tf_cnn_benchmarks.py --model=resnet50 --batch_size=64  --variable_update=horovod"
    ```

2.  Run the following command to query the state of the job:

    ```
    arena get tensorflow-4-resnet50 --type mpijob
    ```

    Expected output:

    ```
    Name:      tensorflow-4-resnet50
    Status:    RUNNING
    Namespace: default
    Priority:  N/A
    Trainer:   MPIJOB
    Duration:  9s
    
    Instances:
      NAME                                  STATUS   AGE  IS_CHIEF  GPU(Requested)  NODE
      ----                                  ------   ---  --------  --------------  ----
      tensorflow-4-resnet50-launcher-q24hv  Running  9s   true      0               cn-shanghai.192.168.16.172
      tensorflow-4-resnet50-worker-0        Running  9s   false     1               cn-shanghai.192.168.16.172
      tensorflow-4-resnet50-worker-1        Running  9s   false     1               cn-shanghai.192.168.16.173
      tensorflow-4-resnet50-worker-2        Running  9s   false     1               cn-shanghai.192.168.16.172
      tensorflow-4-resnet50-worker-3        Running  9s   false     1               cn-shanghai.192.168.16.173
    ```

3.  Run the following command to print the job log:

    ```
    arena logs -f tensorflow-4-resnet50
    ```

    Expected output:

    ```
    total images/sec: 745.38
    ```


## Performance comparison

The following figure shows the performance comparison between GPU topology-aware scheduling and common GPU scheduling based on the preceding examples.

![GPU31](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5201579161/p264372.png)

The figure shows that after GPU topology-aware scheduling is enabled, the TensorFlow distributed jobs are significantly accelerated.

**Note:** The improvement that is achieved by GPU topology-aware scheduling varies based on the models that you use and the cluster environment. You can evaluate the performance of your models based on the preceding examples.

[Overview](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/GPU Resource scheduling/GPU topology-aware scheduling/Overview.md)

[Install the ack-ai-installer component](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/GPU Resource scheduling/GPU topology-aware scheduling/Install the ack-ai-installer component.md)

