---
keyword: [GPU, memory isolation capability of cGPU]
---

# Disable the memory isolation capability of cGPU

This topic describes how to disable the memory isolation capability of cGPU by using a sample application.

[Install the cGPU component](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/Shared GPU scheduling/Install the cGPU component.md)

## Procedure

1.  Run the following command to query information about GPU sharing in your cluster:

    ```
    kubectl inspect cgpu
    ```

    ```
    NAME                      IPADDRESS      GPU0(Allocated/Total)  GPU Memory(GiB)
    cn-beijing.192.16x.x.xx3  192.16x.x.xx3  0/15                   0/15
    cn-beijing.192.16x.x.xx1  192.16x.x.xx1  0/15                   0/15
    cn-beijing.192.16x.x.xx2  192.16x.x.xx2  0/15                   0/15
    ---------------------------------------------------------------------
    Allocated/Total GPU Memory In Cluster:
    0/45 (0%)
    ```

    **Note:** To query detailed information about GPU sharing, run the `kubectl inspect cgpu -d` command.

2.  Use the following YAML template to create a container for which GPU sharing is enabled and memory isolation is disabled:

    ```
    apiVersion: apps/v1
    kind: StatefulSet
    
    metadata:
      name: binpack
      labels:
        app: binpack
    
    spec:
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
    
        spec:
          containers:
          - name: binpack-1
            image: registry.cn-shanghai.aliyuncs.com/tensorflow-samples/tensorflow-gpu-mem:10.0-runtime-centos7
            command:
              - python3
              - /app/main.py
            env:
              - name: CGPU_DISABLE #Disable the memory isolation capability of cGPU. 
                value: "true"
            resources:
              limits:
                # GiB
                aliyun.com/gpu-mem: 3
    ```

    **Note:**

    -   aliyun.com/gpu-mem: specifies the size of GPU memory.
    -   To disable the memory isolation capability of cGPU, set CGPU\_DISABLE to true.
3.  Run the following command to query the result of GPU scheduling performed by cGPU:

    ```
    kubectl inspect cgpu
    ```

    ```
    NAME                      IPADDRESS      GPU0(Allocated/Total)  GPU Memory(GiB)
    cn-beijing.192.16x.x.xx1  192.16x.x.xx1  0/15                   0/15
    cn-beijing.192.16x.x.xx2  192.16x.x.xx2  0/15                   0/15
    cn-beijing.192.16x.x.xx3  192.16x.x.xx3  3/15                   3/15
    ---------------------------------------------------------------------
    Allocated/Total GPU Memory In Cluster:
    3/45 (6%)
    ```

    The newly created container is allocated with 3 GiB of GPU memory from the cn-beijing.192.16x.x.xx3 node.


## Check the result

You can use one of the following methods to check whether the memory isolation capability of cGPU is disabled:

-   Method 1: Run the following command to query the application log:

    ```
    kubectl logs binpack-0 --tail=1
    ```

    ```
    2020-08-25 08:14:54.927965: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1326] Created TensorFlow device (/job:localhost/replica:0/task:0/device:GPU:0 with 15024 MB memory) -> physical GPU (device: 0, name: Tesla V100-SXM2-16GB, pci bus id: 0000:00:07.0, compute capability: 7.0)
    ```

    The returned log entry shows that the GPU memory available for the containerized application is 15,024 MiB. This indicates that the memory isolation capability of cGPU is disabled. If the memory isolation capability of cGPU is enabled, the amount of GPU memory that the containerized application can use is 3 GiB.

-   Method 2: Run the following command to log on to the container and view the amount of GPU memory that is allocated to the container:

    ```
    kubectl exec binpack-0 nvidia-smi
    ```

    ```
    Tue Aug 25 08:23:33 2020
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 418.87.01    Driver Version: 418.87.01    CUDA Version: 10.1     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |===============================+======================+======================|
    |   0  Tesla V100-SXM2...  Off  | 00000000:00:07.0 Off |                    0 |
    | N/A   33C    P0    55W / 300W |  15453MiB / 16130MiB |      1%      Default |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                       GPU Memory |
    |  GPU       PID   Type   Process name                             Usage      |
    |=============================================================================|
    +-----------------------------------------------------------------------------+
    ```

    The output shows that the GPU memory capacity of the host is 16,130 MiB and the amount of GPU memory that is allocated to the container is 15,453 MiB. This indicates that the memory isolation capability of cGPU is disabled. If the memory isolation capability of cGPU is enabled, the amount of GPU memory that is allocated to the container is 3 GiB.


**Related topics**  


[Overview of cGPU](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/Shared GPU scheduling/Overview of cGPU.md)

[Install the cGPU component](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/Shared GPU scheduling/Install the cGPU component.md)

[Monitor and isolate GPU resources](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/Shared GPU scheduling/Monitor and isolate GPU resources.md)

