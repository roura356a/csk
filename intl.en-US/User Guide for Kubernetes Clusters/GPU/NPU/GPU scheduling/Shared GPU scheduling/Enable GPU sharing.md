---
keyword: [cGPU, enable GPU sharing]
---

# Enable GPU sharing

This topic describes how to use GPU sharing in a Container Service for Kubernetes \(ACK\) cluster after you install the cGPU component.

[Install the cGPU component](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/Shared GPU scheduling/Install the cGPU component.md)

**Note:**

-   Only dedicated Kubernetes clusters with GPU-accelerated nodes support the cGPU component. Managed Kubernetes clusters with GPU-accelerated nodes do not support the cGPU component.
-   If you want to use GPU sharing in professional Kubernetes clusters, see [Enable GPU sharing](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/GPU scheduling/cGPU Professional Edition/Enable GPU sharing.md).

## Procedure

1.  Log on to a master node and run the following command to query the status of GPU sharing in your Kubernetes cluster.

    **Note:**

    -   For more information about how to log on to a master node, see [Connect to a Linux instance by using password authentication](/intl.en-US/Instance/Connect to instances/Connect to an instance by using VNC/Connect to a Linux instance by using password authentication.md) and [Connect to a Windows instance by using password authentication](/intl.en-US/Instance/Connect to instances/Connect to an instance by using VNC/Connect to a Windows instance by using password authentication.md).
    -   If you want to query the GPU sharing status of a cluster from an on-premises machine, you must install the cGPU component and tools for managing cGPU. For more information, see [Step 4: Install and use the GPU scheduling inspection tool \(Optional\)](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/GPU scheduling/cGPU Professional Edition/Install and use ack-ai-installer and the GPU scheduling inspection tool.md).
    ```
    kubectl inspect cgpu
    ```

    Expected output:

    ```
    NAME                     IPADDRESS    GPU0(Allocated/Total)  GPU1(Allocated/Total)  GPU Memory(GiB)
    cn-shanghai.192.168.0.4  192.168.0.4  0/7                    0/7                    0/14
    ---------------------------------------------------------------------
    Allocated/Total GPU Memory In Cluster:
    0/14 (0%)
    ```

    **Note:** To query detailed information about GPU sharing, run the kubectl inspect cgpu -d command.

2.  Deploy a sample application that has GPU sharing enabled and request 3 GB of GPU memory for the application.

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
            resources:
              limits:
                # GiB
                aliyun.com/gpu-mem: 3
    ```

    **Note:** aliyun.com/gpu-mem: specifies the requested amount of GPU memory.

3.  Run the following command to query the memory usage of the GPU:

    ```
    kubectl inspect cgpu
    ```

    Expected output:

    ```
    NAME                      IPADDRESS      GPU0(Allocated/Total)  GPU Memory(GiB)
    cn-beijing.192.168.1.105  192.168.1.105  3/14                   3/14
    ---------------------------------------------------------------------
    Allocated/Total GPU Memory In Cluster:
    3/14 (21%)
    ```

    The output shows that the total GPU memory of the cn-beijing.192.168.1.105 node is 14 GB and 3 GB of GPU memory is allocated.


You can use the following method to check whether cGPU has isolated the GPU memory that is allocated to different containers:

-   Run the following command to view the log of the application that is deployed in Step 2.

    You can check whether GPU memory is isolated by cGPU based on the log data.

    ```
    kubectl logs binpack-0 --tail=1
    ```

    Expected output:

    ```
    2020-03-13 09:14:13.931003: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1326] Created TensorFlow device (/job:localhost/replica:0/task:0/device:GPU:0 with 2832 MB memory) -> physical GPU (device: 0, name: Tesla T4, pci bus id: 0000:00:07.0, compute capability: 7.5)
    ```

    The output indicates that the container requests 2,832 MiB of GPU memory.

-   Run the following command to log on to the container and view the amount of GPU memory that is allocated to the container:

    ```
    kubectl exec -it binpack-0 nvidia-smi
    ```

    Expected output:

    ```
    Fri Mar 13 09:32:18 2020
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 418.87.01    Driver Version: 418.87.01    CUDA Version: 10.1     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00000000:00:07.0 Off |                    0 |
    | N/A   41C    P0    26W /  70W |   3043MiB /  3231MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                       GPU Memory |
    |  GPU       PID   Type   Process name                             Usage      |
    |=============================================================================|
    +-----------------------------------------------------------------------------+
    ```

    The output indicates that the amount of GPU memory allocated to the container is 3,231 MiB.

-   Run the following command to query the total GPU memory of the node where the application is deployed. Perform this operation on the node.

    ```
    nvidia-smi
    ```

    Expected output:

    ```
    Fri Mar 13 17:36:24 2020
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 418.87.01    Driver Version: 418.87.01    CUDA Version: 10.1     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00000000:00:07.0 Off |                    0 |
    | N/A   40C    P0    26W /  70W |   3053MiB / 15079MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                       GPU Memory |
    |  GPU       PID   Type   Process name                             Usage      |
    |=============================================================================|
    |    0      8796      C   python3                                     3043MiB |
    +-----------------------------------------------------------------------------+
    
                            
    ```

    The output indicates that the total GPU memory of the host is 15,079 MiB and the amount of GPU memory that is allocated to the container is 3,053 MiB.


**Related topics**  


[Overview of cGPU](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/Shared GPU scheduling/Overview of cGPU.md)

[Install the cGPU component](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/Shared GPU scheduling/Install the cGPU component.md)

[Monitor and isolate GPU resources](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/Shared GPU scheduling/Monitor and isolate GPU resources.md)

