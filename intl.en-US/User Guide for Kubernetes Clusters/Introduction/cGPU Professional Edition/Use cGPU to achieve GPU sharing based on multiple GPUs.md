---
keyword: [GPU sharing, GPU sharing based on multiple GPUs, cGPU]
---

# Use cGPU to achieve GPU sharing based on multiple GPUs

You can use cGPU in professional Kubernetes clusters to achieve graphics processing unit \(GPU\) sharing and memory isolation. This topic describes how to use cGPU to achieve GPU sharing based on multiple GPUs.

[t1997635.md\#]()

GPU sharing based on multiple GPUs works in the following way: an application requests N GiB of GPU memory in total and requires M GPUs to allocate the requested amount of memory. The memory that is allocated by each GPU is N/M. The value of N/M must be an integer and the used GPUs must be installed on the same node. For example, an application requests 8 GiB of GPU memory and requires that the memory is allocated by 4 GPUs. In this case, the application is allocated with 8 GiB of GPU memory from 4 GPUs that are installed on the same node. Each GPU allocates 2 GiB of memory.

## Procedure

1.  Create a file named binpack-1.yaml.

    Create a StatefulSet application named binpack-1 by using a YAML file and add the following settings to the YAML file:

    -   Set the number of pod replicas to 1.
    -   Declare 4 GPUs for each pod and specify that each GPU allocates 2 GiB of memory.
        -   Add label `aliyun.com/gpu-count=4` to the pod.
        -   Set the number of extended resources `aliyun.com/gpu-mem` to 8 for the pod.
    ```
    apiVersion: apps/v1
    kind: StatefulSet
    
    metadata:
      name: binpack-1
      labels:
        app: binpack-1
    
    spec:
      # Set the number of pod replicas to 1. This makes it easier for you to check GPU memory allocation.
      replicas: 1
      serviceName: "binpack-1"
      podManagementPolicy: "Parallel"
      selector: # define how the deployment finds the pods it manages
        matchLabels:
          app: binpack-1
    
      template: # define the pods specifications
        metadata:
          labels:
            app: binpack-1
            # Declare 4 GPUs for the pod and specify that each GPU allocates 2 GiB of memory.
            aliyun.com/gpu-count: "4"
        spec:
          containers:
          - name: binpack-1
            image: registry.cn-shanghai.aliyuncs.com/tensorflow-samples/tensorflow-gpu-mem:10.0-runtime-centos7
            resources:
              limits:
                # The pod requests 8 GiB of GPU memory in total.
                aliyun.com/gpu-mem: 8
    ```

2.  Deploy application binpack-1.

    ```
    kubectl apply -f binpack-1.yaml
    ```


## Verify the result.

1.  Run the following command to query the state of the pod:

    ```
    kubectl get po
    ```

    Expected output:

    ```
    NAME               READY   STATUS      RESTARTS   AGE
    binpack-1-0        1/1     Running     0          9m34s
    ```

2.  Run the following command to query the number of used GPUs and the amount of memory that is allocated by each GPU:

    ```
    kubectl exec binpack-1-0 -- nvidia-smi
    ```

    Expected output:

    ```
    Tue Nov  3 09:05:05 2020
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 418.87.01    Driver Version: 418.87.01    CUDA Version: 10.1     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |===============================+======================+======================|
    |   0  Tesla V100-SXM2...  On   | 00000000:00:07.0 Off |                    0 |
    | N/A   31C    P0    54W / 300W |   2084MiB /  2150MiB |      1%      Default |
    +-------------------------------+----------------------+----------------------+
    |   1  Tesla V100-SXM2...  On   | 00000000:00:08.0 Off |                    0 |
    | N/A   32C    P0    55W / 300W |   2084MiB /  2150MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    |   2  Tesla V100-SXM2...  On   | 00000000:00:09.0 Off |                    0 |
    | N/A   32C    P0    54W / 300W |   2084MiB /  2150MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    |   3  Tesla V100-SXM2...  On   | 00000000:00:0A.0 Off |                    0 |
    | N/A   34C    P0    54W / 300W |   2084MiB /  2150MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                       GPU Memory |
    |  GPU       PID   Type   Process name                             Usage      |
    |=============================================================================|
    +-----------------------------------------------------------------------------+
    ```

    The preceding output shows that 4 GPUs are used and the amount of memory that is allocated by each GPU almost equals the applied amount: 2 GiB.

    **Note:** The amount of memory that is allocated by each GPU may be tens to hundreds of megabytes less than the applied amount.

3.  Run the following command to query the GPUs that are used by the container:

    ```
    kubectl exec binpack-1-0 -- env | grep NVIDIA_VISIBLE_DEVICES
    ```

    Expected output:

    ```
    NVIDIA_VISIBLE_DEVICES=GPU-c09d11f5-a565-1f83-903c-a65a7ba138af,GPU-959873c5-b57b-5d24-f3aa-e700c5d431cb,GPU-abdcd4da-7505-c267-fb4d-30478a360e07,GPU-ba997778-f8ce-e433-3c84-6d15392a06a1
    ```

4.  You can use environment variable `ALIYUN_COM_GPU_MEM_CONTAINER` to query the amount of memory that can be allocated from each GPU to the container.

    ```
    kubectl exec binpack-1-0 -- env | grep ALIYUN
    ```

    Expected output:

    ```
    ALIYUN_COM_GPU_MEM_CONTAINER=2 # The amount of memory that can be allocated from each GPU to the container is 2 GiB. The application that is deployed in the container can read the value to set memory limit for the container.
    ALIYUN_COM_GPU_MEM_DEV=15 # The total memory of each GPU is 15 GiB.
    ```


