---
keyword: [GPU topology-aware scheduling, Kubernetes scheduling, deep learning, PyTorch distributed jobs]
---

# Use GPU topology-aware scheduling to achieve optimal GPU acceleration for PyTorch distributed jobs

Based on the scheduling framework, Container Service for Kubernetes \(ACK\) supports GPU topology-aware scheduling. This feature selects a combination of GPUs from GPU-accelerated nodes to achieve optimal GPU acceleration for training jobs. This topic describes how to use GPU topology-aware scheduling to achieve optimal GPU acceleration for PyTorch distributed jobs.

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

Submit a Message Passing Interface \(MPI\) job and set --gputopology to `true`.

```
arena submit mpi --gputopology=true ***
```

## Example 1: Train VGG16

**Note:** In this topic, two V100 servers are deployed in the test cluster. Each V100 server has 8 GPUs.

**Use GPU topology-aware scheduling to train VGG16**

1.  Run the following command to submit a job to the cluster:

    ```
    arena submit mpi \
      --name=pytorch-topo-4-vgg16 \
      --gpus=1 \
      --workers=4 \
      --gputopology=true \
      --image=registry.cn-hangzhou.aliyuncs.com/kubernetes-image-hub/pytorch-benchmark:torch1.6.0-py3.7-cuda10.1 \
      "mpirun --allow-run-as-root -np "4" -bind-to none -map-by slot -x NCCL_DEBUG=INFO -x NCCL_SOCKET_IFNAME=eth0 -x LD_LIBRARY_PATH -x PATH --mca pml ob1 --mca btl_tcp_if_include eth0 --mca oob_tcp_if_include eth0 --mca orte_keep_fqdn_hostnames t --mca btl ^openib python /examples/pytorch_synthetic_benchmark.py --model=vgg16 --batch-size=64"
    ```

2.  Run the following command to query the state of the job:

    ```
    arena get pytorch-topo-4-vgg16 --type mpijob
    ```

    Expected output:

    ```
    Name:      pytorch-topo-4-vgg16
    Status:    RUNNING
    Namespace: default
    Priority:  N/A
    Trainer:   MPIJOB
    Duration:  11s
    
    Instances:
      NAME                                 STATUS   AGE  IS_CHIEF  GPU(Requested)  NODE
      ----                                 ------   ---  --------  --------------  ----
      pytorch-topo-4-vgg16-launcher-mnjzr  Running  11s  true      0               cn-shanghai.192.168.16.173
      pytorch-topo-4-vgg16-worker-0        Running  11s  false     1               cn-shanghai.192.168.16.173
      pytorch-topo-4-vgg16-worker-1        Running  11s  false     1               cn-shanghai.192.168.16.173
      pytorch-topo-4-vgg16-worker-2        Running  11s  false     1               cn-shanghai.192.168.16.173
      pytorch-topo-4-vgg16-worker-3        Running  11s  false     1               cn-shanghai.192.168.16.173
    ```

3.  Run the following command to print the job log:

    ```
    arena logs -f pytorch-topo-4-vgg16
    ```

    Expected output:

    ```
    Model: vgg16
    Batch size: 64
    Number of GPUs: 4
    Running warmup...
    Running benchmark...
    Iter #0: 205.5 img/sec per GPU
    Iter #1: 205.2 img/sec per GPU
    Iter #2: 205.1 img/sec per GPU
    Iter #3: 205.5 img/sec per GPU
    Iter #4: 205.1 img/sec per GPU
    Iter #5: 205.1 img/sec per GPU
    Iter #6: 205.3 img/sec per GPU
    Iter #7: 204.3 img/sec per GPU
    Iter #8: 205.0 img/sec per GPU
    Iter #9: 204.9 img/sec per GPU
    Img/sec per GPU: 205.1 +-0.6
    Total img/sec on 4 GPU(s): 820.5 +-2.5
    ```


**Use common GPU scheduling to train VGG16**

1.  Run the following command to submit a job to the cluster:

    ```
    arena submit mpi \
      --name=pytorch-4-vgg16 \
      --gpus=1 \
      --workers=4 \
      --image=registry.cn-hangzhou.aliyuncs.com/kubernetes-image-hub/pytorch-benchmark:torch1.6.0-py3.7-cuda10.1 \
      "mpirun --allow-run-as-root -np "4" -bind-to none -map-by slot -x NCCL_DEBUG=INFO -x NCCL_SOCKET_IFNAME=eth0 -x LD_LIBRARY_PATH -x PATH --mca pml ob1 --mca btl_tcp_if_include eth0 --mca oob_tcp_if_include eth0 --mca orte_keep_fqdn_hostnames t --mca btl ^openib python /examples/pytorch_synthetic_benchmark.py --model=vgg16 --batch-size=64"
    ```

2.  Run the following command to query the state of the job:

    ```
    arena get pytorch-4-vgg16 --type mpijob
    ```

    Expected output:

    ```
    Name:      pytorch-4-vgg16
    Status:    RUNNING
    Namespace: default
    Priority:  N/A
    Trainer:   MPIJOB
    Duration:  10s
    
    Instances:
      NAME                            STATUS   AGE  IS_CHIEF  GPU(Requested)  NODE
      ----                            ------   ---  --------  --------------  ----
      pytorch-4-vgg16-launcher-qhnxl  Running  10s  true      0               cn-shanghai.192.168.16.173
      pytorch-4-vgg16-worker-0        Running  10s  false     1               cn-shanghai.192.168.16.173
      pytorch-4-vgg16-worker-1        Running  10s  false     1               cn-shanghai.192.168.16.173
      pytorch-4-vgg16-worker-2        Running  10s  false     1               cn-shanghai.192.168.16.173
      pytorch-4-vgg16-worker-3        Running  10s  false     1               cn-shanghai.192.168.16.173
    ```

3.  Run the following command to print the job log:

    ```
    arena logs -f pytorch-4-vgg16
    ```

    Expected output:

    ```
    Model: vgg16
    Batch size: 64
    Number of GPUs: 4
    Running warmup...
    Running benchmark...
    Iter #0: 113.1 img/sec per GPU
    Iter #1: 109.5 img/sec per GPU
    Iter #2: 106.5 img/sec per GPU
    Iter #3: 108.5 img/sec per GPU
    Iter #4: 108.1 img/sec per GPU
    Iter #5: 111.2 img/sec per GPU
    Iter #6: 110.7 img/sec per GPU
    Iter #7: 109.8 img/sec per GPU
    Iter #8: 102.8 img/sec per GPU
    Iter #9: 107.9 img/sec per GPU
    Img/sec per GPU: 108.8 +-5.3
    Total img/sec on 4 GPU(s): 435.2 +-21.1
    ```


## Example 2: Train ResNet50

**Use GPU topology-aware scheduling to train ResNet50**

1.  Run the following command to submit a job to the cluster:

    ```
    arena submit mpi \
      --name=pytorch-topo-4-resnet50 \
      --gpus=1 \
      --workers=4 \
      --gputopology=true \
      --image=registry.cn-hangzhou.aliyuncs.com/kubernetes-image-hub/pytorch-benchmark:torch1.6.0-py3.7-cuda10.1 \
      "mpirun --allow-run-as-root -np "4" -bind-to none -map-by slot -x NCCL_DEBUG=INFO -x NCCL_SOCKET_IFNAME=eth0 -x LD_LIBRARY_PATH -x PATH --mca pml ob1 --mca btl_tcp_if_include eth0 --mca oob_tcp_if_include eth0 --mca orte_keep_fqdn_hostnames t --mca btl ^openib python /examples/pytorch_synthetic_benchmark.py --model=resnet50 --batch-size=64"
    ```

2.  Run the following command to query the state of the job:

    ```
    arena get pytorch-topo-4-resnet50 --type mpijob
    ```

    Expected output:

    ```
    Name:      pytorch-topo-4-resnet50
    Status:    RUNNING
    Namespace: default
    Priority:  N/A
    Trainer:   MPIJOB
    Duration:  8s
    
    Instances:
      NAME                                    STATUS   AGE  IS_CHIEF  GPU(Requested)  NODE
      ----                                    ------   ---  --------  --------------  ----
      pytorch-topo-4-resnet50-launcher-x7r2n  Running  8s   true      0               cn-shanghai.192.168.16.173
      pytorch-topo-4-resnet50-worker-0        Running  8s   false     1               cn-shanghai.192.168.16.173
      pytorch-topo-4-resnet50-worker-1        Running  8s   false     1               cn-shanghai.192.168.16.173
      pytorch-topo-4-resnet50-worker-2        Running  8s   false     1               cn-shanghai.192.168.16.173
      pytorch-topo-4-resnet50-worker-3        Running  8s   false     1               cn-shanghai.192.168.16.173
    ```

3.  Run the following command to print the job log:

    ```
    arena logs -f pytorch-topo-4-resnet50
    ```

    Expected output:

    ```
    Model: resnet50
    Batch size: 64
    Number of GPUs: 4
    Running warmup...
    Running benchmark...
    Iter #0: 331.0 img/sec per GPU
    Iter #1: 330.6 img/sec per GPU
    Iter #2: 330.9 img/sec per GPU
    Iter #3: 330.4 img/sec per GPU
    Iter #4: 330.7 img/sec per GPU
    Iter #5: 330.8 img/sec per GPU
    Iter #6: 329.9 img/sec per GPU
    Iter #7: 330.5 img/sec per GPU
    Iter #8: 330.4 img/sec per GPU
    Iter #9: 329.7 img/sec per GPU
    Img/sec per GPU: 330.5 +-0.8
    Total img/sec on 4 GPU(s): 1321.9 +-3.2
    ```


**Use common GPU scheduling to train ResNet50**

1.  Run the following command to submit a job to the cluster:

    ```
    arena submit mpi \
      --name=pytorch-4-resnet50 \
      --gpus=1 \
      --workers=4 \
      --image=registry.cn-hangzhou.aliyuncs.com/kubernetes-image-hub/pytorch-benchmark:torch1.6.0-py3.7-cuda10.1 \
      "mpirun --allow-run-as-root -np "4" -bind-to none -map-by slot -x NCCL_DEBUG=INFO -x NCCL_SOCKET_IFNAME=eth0 -x LD_LIBRARY_PATH -x PATH --mca pml ob1 --mca btl_tcp_if_include eth0 --mca oob_tcp_if_include eth0 --mca orte_keep_fqdn_hostnames t --mca btl ^openib python /examples/pytorch_synthetic_benchmark.py --model=resnet50 --batch-size=64"
    ```

2.  Run the following command to query the state of the job:

    ```
    arena get pytorch-4-resnet50 --type mpijob
    ```

    Expected output:

    ```
    Name:      pytorch-4-resnet50
    Status:    RUNNING
    Namespace: default
    Priority:  N/A
    Trainer:   MPIJOB
    Duration:  10s
    
    Instances:
      NAME                               STATUS   AGE  IS_CHIEF  GPU(Requested)  NODE
      ----                               ------   ---  --------  --------------  ----
      pytorch-4-resnet50-launcher-qw5k6  Running  10s  true      0               cn-shanghai.192.168.16.173
      pytorch-4-resnet50-worker-0        Running  10s  false     1               cn-shanghai.192.168.16.173
      pytorch-4-resnet50-worker-1        Running  10s  false     1               cn-shanghai.192.168.16.173
      pytorch-4-resnet50-worker-2        Running  10s  false     1               cn-shanghai.192.168.16.173
      pytorch-4-resnet50-worker-3        Running  10s  false     1               cn-shanghai.192.168.16.173
    ```

3.  Run the following command to print the job log:

    ```
    arena logs -f pytorch-4-resnet50
    ```

    Expected output:

    ```
    Model: resnet50
    Batch size: 64
    Number of GPUs: 4
    Running warmup...
    Running benchmark...
    Iter #0: 313.1 img/sec per GPU
    Iter #1: 312.8 img/sec per GPU
    Iter #2: 313.0 img/sec per GPU
    Iter #3: 312.2 img/sec per GPU
    Iter #4: 313.7 img/sec per GPU
    Iter #5: 313.2 img/sec per GPU
    Iter #6: 313.6 img/sec per GPU
    Iter #7: 313.0 img/sec per GPU
    Iter #8: 311.3 img/sec per GPU
    Iter #9: 313.6 img/sec per GPU
    Img/sec per GPU: 313.0 +-1.3
    Total img/sec on 4 GPU(s): 1251.8 +-5.3
    ```


## Performance comparison

The following figure shows the performance comparison between GPU topology-aware scheduling and common GPU scheduling based on the preceding examples.

![gpu32](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1835579161/p264379.png)

The figure shows that after GPU topology-aware scheduling is activated, the PyTorch distributed jobs are significantly accelerated.

**Note:** The improvement that is achieved by GPU topology-aware scheduling varies based on the models that you use and the cluster environment. You can evaluate the performance of your models based on the preceding examples.

[Overview](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/GPU Resource scheduling/GPU topology-aware scheduling/Overview.md)

[Install the ack-ai-installer component](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/GPU Resource scheduling/GPU topology-aware scheduling/Install the ack-ai-installer component.md)

