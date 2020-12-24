---
keyword: [cGPU memory isolation, containers that share a GPU, examples]
---

# Enable GPU sharing

This topic describes how to deploy a YAML file to create containers that share one graphics processing unit \(GPU\). After you deploy the file, you can use cGPU to isolate the GPU memory that is allocated to each container. This improves GPU resource utilization.

[t1997635.md\#]()

## Procedure

1.  Run the following command to query information about GPU sharing in your cluster:

    ```
    kubectl inspect cgpu
    ```

    ```
    NAME                     IPADDRESS    GPU0(Allocated/Total)  GPU1(Allocated/Total)  GPU Memory(GiB)
    cn-shanghai.192.168.0.4  192.168.0.4  0/7                    0/7                    0/14
    ---------------------------------------------------------------------
    Allocated/Total GPU Memory In Cluster:
    0/14 (0%)
    ```

    **Note:** To query detailed information about GPU sharing, run the kubectl inspect cgpu -d command.

2.  Deploy the following YAML file to create containers that share one GPU:

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

    **Note:** aliyun.com/gpu-mem: Specify the amount of memory that is allocated to the container.

3.  Run the following command to query the result of resource scheduling performed by cGPU:

    ```
    kubectl inspect cgpu
    ```

    ```
    NAME                      IPADDRESS      GPU0(Allocated/Total)  GPU Memory(GiB)
    cn-beijing.192.168.1.105  192.168.1.105  3/14                   3/14
    ---------------------------------------------------------------------
    Allocated/Total GPU Memory In Cluster:
    3/14 (21%)
    ```


You can use one of the following methods to check whether cGPU isolates the GPU memory.

-   Run the following command to view the log entries that record YAML file deployment in Step 2.

    The log entries provide more information about whether the GPU memory is isolated by cGPU.

    ```
    kubectl logs binpack-0 --tail=1
    2020-03-13 09:14:13.931003: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1326] Created TensorFlow device (/job:localhost/replica:0/task:0/device:GPU:0 with 2832 MB memory) -> physical GPU (device: 0, name: Tesla T4, pci bus id: 0000:00:07.0, compute capability: 7.5)
    ```

    The command output indicates that the container applies for GPU memory of 2,832 MiB.

-   Run the following command to log on to the container and view the memory that is allocated to the container:

    ```
    kubectl exec -it binpack-0 nvidia-smi
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

    The command output indicates that the memory allocated to the container is 3,231 MiB \(3 x 1,024 = 3,072\).

-   Run the following command to view the total memory of the host:

    ```
    nvidia-smi
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

    The command output indicates that the total memory of the host is 15,079 MiB and the memory that is allocated to the container is 3,053 MiB.


