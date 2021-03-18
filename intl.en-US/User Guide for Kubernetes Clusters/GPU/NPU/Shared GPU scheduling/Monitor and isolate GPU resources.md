---
keyword: [cGPU, managed Prometheus monitoring, GPU resource isolation]
---

# Monitor and isolate GPU resources

Container Service for Kubernetes \(ACK\) allows you to install the managed Prometheus plug-in. You can use the plug-in to monitor the graphics processing unit \(GPU\) resources. The cGPU solution isolates the GPU resources allocated to containers that share one GPU. You do not have to modify the existing GPU program. This topic describes how to monitor the GPU memory usage by using the managed Prometheus plug-in. It also provides more information about how to isolate GPU resources by using cGPU.

-   A standard dedicated GPU cluster is created and Kubernetes 1.16 or later is used.
-   [Activate and upgrade ARMS](/intl.en-US/Quick Start/Activate and upgrade ARMS.md).
-   Your Alibaba Cloud account is used to log on to the [RAM console](https://ram.console.aliyun.com/). It is authorized to use ARMS Prometheus.
-   The GPU hardware is Tesla P4, Tesla P100, Tesla T4, or Tesla V100 \(16 GB\).

The development of artificial intelligence \(AI\) is fueled by high GPU hash rates, large amounts of data, and optimized algorithms. NVIDIA GPUs provide common heterogeneous computing techniques. These techniques are the basis for high-performance deep learning. The cost of GPUs is high. If each application uses one GPU, computing resources may be wasted. GPU sharing allows you to improve the resource usage. You must also consider how to achieve the highest query rate at the lowest cost and how to fulfill the application service-level agreement \(SLA\).

## Use the managed Prometheus plug-in to monitor a GPU that is used by one container

1.  Log on to the [ARMS console](https://arms-intl.console.aliyun.com/).

2.  In the left-side navigation pane, click **Prometheus Monitoring**.

3.  On the **Prometheus Monitoring** page, select the region where the cluster is deployed, and click **Install** in the **Actions** column of the cluster.

4.  In the **Confirm** dialog box, click **OK**.

    It requires about two minutes to install the Prometheus plug-in. After the Prometheus plug-in is installed, it appears in the **Installed plugins** column.

5.  You can deploy the following sample application by using the command-line interface \(CLI\). For more information, see [Manage applications by using the CLI](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Manage applications by using the CLI.md).

    ```
    apiVersion: apps/v1beta1
    kind: StatefulSet
    
    metadata:
      name: app-3g-v1
      labels:
        app: app-3g-v1
    spec:
      replicas: 1
      serviceName: "app-3g-v1"
      podManagementPolicy: "Parallel"
      selector: # define how the deployment finds the pods it manages
        matchLabels:
          app: app-3g-v1
      updateStrategy:
        type: RollingUpdate
      template: # define the pods specifications
        metadata:
          labels:
            app: app-3g-v1
        spec:
          containers:
          - name: app-3g-v1
            image: registry.cn-shanghai.aliyuncs.com/tensorflow-samples/cuda-malloc:3G
            resources:
              limits:
                nvidia.com/gpu: 1
    ```

    After the application is deployed, run the following command to check the application status. The command output indicates that the application name is app-3g-v1-0.

    ```
    kubectl get po
    ```

    ```
    NAME          READY   STATUS    RESTARTS   AGE
    app-3g-v1-0   1/1     Running   1          2m56s
    ```

6.  Click **GPU APP** in the **Actions** column of the cluster.

    The following figure shows that the application uses only 20% of the GPU memory. This indicates that 80% of the total memory is wasted. The total memory of the GPU is about 16 GB. However, the used memory stabilizes at about 3.4 GB. If you allocate one GPU to each application, a high number of GPU resources are wasted. To improve the GPU usage, you can use cGPU to deploy multiple applications on one GPU.

    ![GPU memory usage](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7935359951/p101791.png)


## Use cGPU to enable GPU sharing among multiple containers

1.  Label the nodes on which GPUs are installed.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane, click **Clusters**.

    3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Applications** in the **Actions** column.

    4.  In the left-side navigation pane, click **Nodes**.

    5.  On the **Nodes** page, click **Manage Labels and Taints**.

    6.  In the left-side navigation pane, choose **Clusters** \> **Nodes**.

    7.  On the **Nodes** page, select the cluster that you want to manage and click **Manage Labels and Taints** in the upper-right corner of the page.

    8.  On the **Manage Labels and Taints** page, select the nodes to which you want to add a label and click **Add Label**.

    9.  In the **Add** dialog box, specify cgpu as the label name. Then, set the value of cgpu to true, and click **OK**.

        ![Add a label](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7935359951/p101645.png)

        **Note:** If the label cgpu=true is added to a worker node, the worker node does not use the dedicated GPU resource **nvidia.com/gpu**. To disable GPU sharing for the worker node, set the value of cgpu to false. This allows the worker node to use the dedicated GPU resource **nvidia.com/gpu**.

2.  Install cGPU.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane, choose **Marketplace** \> **App Catalog**. Then, click **ack-cgpu** on the right of the page.

    3.  In the **Deploy** section on the right of the page, select the required cluster and namespace and click **Create**.

    4.  Log on to the master node and run the following commands to view the GPU resources.

        For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md).

        ```
        kubectl inspect cgpu
        ```

        ```
        NAME                       IPADDRESS      GPU0(Allocated/Total)  GPU Memory(GiB)
        cn-hangzhou.192.168.2.167  192.168.2.167  0/15                   0/15
        ----------------------------------------------------------------------
        Allocated/Total GPU Memory In Cluster:
        0/15 (0%)
        ```

        **Note:** The command output indicates that the GPU resources are allocated at the memory level.

3.  Deploy workloads for the shared GPU.

    1.  Modify the YAML file that was used to deploy the cGPU application.

        -   Modify the replicas parameter from 1 to 2. Before the modification, only one pod can be deployed on the GPU. The modification allows you to deploy two pods on the GPU.
        -   Change the resource type from `nvidia.com/gpu` to `aliyun.com/gpu-mem`. The unit of the GPU resource is changed to GiB.
        ```
        apiVersion: apps/v1beta1
        kind: StatefulSet
        
        metadata:
          name: app-3g-v1
          labels:
            app: app-3g-v1
        spec:
          replicas: 2
          serviceName: "app-3g-v1"
          podManagementPolicy: "Parallel"
          selector: # define how the deployment finds the pods it manages
            matchLabels:
              app: app-3g-v1
          template: # define the pods specifications
            metadata:
              labels:
                app: app-3g-v1
            spec:
              containers:
              - name: app-3g-v1
                image: registry.cn-shanghai.aliyuncs.com/tensorflow-samples/cuda-malloc:3G
                resources:
                  limits:
                    aliyun.com/gpu-mem: 4
        ```

    2.  Create new workloads for the shared GPU based on memory allocation.

        The command output indicates that the two pods are running on the same GPU.

        ```
        kubectl inspect cgpu -d
        ```

        ```
        NAME:       cn-hangzhou.192.168.2.167
        IPADDRESS:  192.168.2.167
        
        NAME         NAMESPACE  GPU0(Allocated)
        app-3g-v1-0  default    4
        app-3g-v1-1  default    4
        Allocated :  8 (53%)
        Total :      15
        --------------------------------------------------------
        
        Allocated/Total GPU Memory In Cluster:  8/15 (53%)
        ```

    3.  Run the following commands to log on to each of the containers.

        The command outputs indicate that the maximum GPU memory that can be used by each container is 4,301 MiB.

        ```
        kubectl exec -it app-3g-v1-0 nvidia-smi
        Mon Apr 13 01:33:10 2020
        +-----------------------------------------------------------------------------+
        | NVIDIA-SMI 418.87.01    Driver Version: 418.87.01    CUDA Version: 10.1     |
        |-------------------------------+----------------------+----------------------+
        | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
        | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
        |===============================+======================+======================|
        |   0  Tesla V100-SXM2...  On   | 00000000:00:07.0 Off |                    0 |
        | N/A   37C    P0    57W / 300W |   3193MiB /  4301MiB |      0%      Default |
        +-------------------------------+----------------------+----------------------+
        
        +-----------------------------------------------------------------------------+
        | Processes:                                                       GPU Memory |
        |  GPU       PID   Type   Process name                             Usage      |
        |=============================================================================|
        +-----------------------------------------------------------------------------+
        
        kubectl exec -it app-3g-v1-1 nvidia-smi
        Mon Apr 13 01:36:07 2020
        +-----------------------------------------------------------------------------+
        | NVIDIA-SMI 418.87.01    Driver Version: 418.87.01    CUDA Version: 10.1     |
        |-------------------------------+----------------------+----------------------+
        | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
        | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
        |===============================+======================+======================|
        |   0  Tesla V100-SXM2...  On   | 00000000:00:07.0 Off |                    0 |
        | N/A   38C    P0    57W / 300W |   3193MiB /  4301MiB |      0%      Default |
        +-------------------------------+----------------------+----------------------+
        
        +-----------------------------------------------------------------------------+
        | Processes:                                                       GPU Memory |
        |  GPU       PID   Type   Process name                             Usage      |
        |=============================================================================|
        +-----------------------------------------------------------------------------+
        ```

    4.  Log on to the GPU node to check the resource usage.

        The command output indicates that the total used GPU memory is 6,396 MiB. This is calculated based on the sum of the memory used by the two containers. This shows the cGPU solution has isolated GPU memory by container. If you log on to a container to apply for more GPU resources, an error message is returned to indicate a memory allocation error.

        ```
        kubectl exec -it app-3g-v1-1 bash
        root@app-3g-v1-1:/# cuda_malloc -size=1024
        cgpu_cuda_malloc starting...
        Detected 1 CUDA Capable device(s)
        
        Device 0: "Tesla V100-SXM2-16GB"
          CUDA Driver Version / Runtime Version          10.1 / 10.1
          Total amount of global memory:                 4301 MBytes (4509925376 bytes)
        Try to malloc 1024 MBytes memory on GPU 0
        CUDA error at cgpu_cuda_malloc.cu:119 code=2(cudaErrorMemoryAllocation) "cudaMalloc( (void**)&dev_c, malloc_size)"
        ```


In the [ARMS console](https://arms-intl.console.aliyun.com/), you can monitor the GPU resources that are used by nodes and applications.

-   GPU APP: You can view the size of the memory usage and the percentage of each application.

    ![GPU App](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7935359951/p101827.png)

-   GPU Node: You can view the size of the memory usage of a GPU.

    ![GPU node](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7935359951/p101828.png)


## Use the managed Prometheus plug-in to monitor a shared GPU

If the memory used by an application exceeds the allocated memory, cGPU ensures that the memory usage of other applications is not affected.

1.  Deploy a new application on the shared GPU.

    The application requests at least 4 GiB of GPU memory. However, the actual memory usage of the application is 6 GiB.

    ```
    apiVersion: apps/v1beta1
    kind: StatefulSet
    
    metadata:
      name: app-6g-v1
      labels:
        app: app-6g-v1
    spec:
      replicas: 1
      serviceName: "app-6g-v1"
      podManagementPolicy: "Parallel"
      selector: # define how the deployment finds the pods it manages
        matchLabels:
          app: app-6g-v1
      template: # define the pods specifications
        metadata:
          labels:
            app: app-6g-v1
        spec:
          containers:
          - name: app-6g-v1
            image: registry.cn-shanghai.aliyuncs.com/tensorflow-samples/cuda-malloc:6G
            resources:
              limits:
                aliyun.com/gpu-mem: 4
    ```

2.  Run the following command to query the pod status.

    The pod on which the new application runs is in the **CrashLoopBackOff** state. The other two pods are running as normal.

    ```
    kubectl get pod
    ```

    ```
    NAME          READY   STATUS             RESTARTS   AGE
    app-3g-v1-0   1/1     Running            0          7h35m
    app-3g-v1-1   1/1     Running            0          7h35m
    app-6g-v1-0   0/1     CrashLoopBackOff   5          3m15s
    ```

3.  Run the following command to check error details in the container logs.

    The command output indicates that the cudaErrorMemoryAllocation error has occurred.

    ```
    kubectl logs app-6g-v1-0
    ```

    ```
    CUDA error at cgpu_cuda_malloc.cu:119 code=2(cudaErrorMemoryAllocation) "cudaMalloc( (void**)&dev_c, malloc_size)"
    ```

4.  Use the GPU APP component of the managed Prometheus plug-in to view the container status.

    The following figure shows that the deployment of the new application does not affect the running of the existing containers.

    ![GPU resource isolation by application](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8935359951/p101900.png)


