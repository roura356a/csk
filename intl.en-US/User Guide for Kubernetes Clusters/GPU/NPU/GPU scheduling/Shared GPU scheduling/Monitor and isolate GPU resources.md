---
keyword: [cGPU, managed Prometheus plug-in for monitoring, isolate GPU resources]
---

# Monitor and isolate GPU resources

Container Service for Kubernetes \(ACK\) allows you to install the managed Prometheus plug-in. You can use the plug-in to monitor GPU resources. You can use the cGPU solution to schedule multiple applications to one GPU and isolate the GPU memory and computing power that are allocated to each application. This topic describes how to monitor GPU memory usage by using the managed Prometheus plug-in. This topic also describes how to isolate GPU resources by using cGPU.

-   A standard dedicated Kubernetes cluster with GPU-accelerated nodes is created and the Kubernetes version is 1.16 or later.
-   [Activate and upgrade ARMS](/intl.en-US/Quick Start/Activate and upgrade ARMS.md).
-   An Alibaba Cloud account is used to log on to the [Resource Access Management \(RAM\) console](https://ram.console.aliyun.com/). The account is authorized to use ARMS Prometheus.
-   The GPU model is Tesla P4, Tesla P100, Tesla T4, or Tesla V100 \(16 GB\).

The development of AI is fueled by high hash rates, large amounts of data, and optimized algorithms. NVIDIA GPUs provide common heterogeneous computing techniques. These techniques are the basis for high-performance deep learning. The cost of GPUs is high. If each application uses one dedicated GPU in prediction scenarios, computing resources may be wasted. GPU sharing improves resource usage. You must consider how to achieve the highest query rate at the lowest cost and how to fulfill the application service level agreement \(SLA\).

## Use the managed Prometheus plug-in to monitor dedicated GPUs

1.  Log on to the[Application Real-Time Monitoring Service \(ARMS\) console](https://arms-intl.console.aliyun.com/).

2.  In the left-side navigation pane, click **Prometheus Monitoring**.

3.  On the **Prometheus Monitoring** page, select the region where the cluster is deployed and click **Install** in the **Actions** column.

4.  In the **Confirmation** message, click **OK**.

    It requires about 2 minutes to install the Prometheus plug-in. After the Prometheus plug-in is installed, it appears in the **Installed Dashboards** column.

5.  You can deploy the following sample application by using a CLI. For more information, see [Manage applications by using commands](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Create a stateless application by using a Deployment.md).

    ```
    apiVersion: apps/v1
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

    After the application is deployed, run the following command to query the state of the application. The output indicates that the application name is app-3g-v1-0.

    ```
    kubectl get po
    ```

    Expected output:

    ```
    NAME          READY   STATUS    RESTARTS   AGE
    app-3g-v1-0   1/1     Running   1          2m56s
    ```

6.  Find and click the cluster where the application is deployed. On the Dashboards page, click **GPU APP** in the **Name** column.

    The following figure shows that the application uses only 20% of the GPU memory, which indicates that 80% of the GPU memory is wasted. The total GPU memory is about 16 GB. However, the memory usage stabilizes at about 3.4 GB. If you allocate one GPU to each application, a large amount of GPU resources are wasted. To improve GPU resource usage, you can use cGPU to share a GPU among multiple applications.

    ![GPU memory usage](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7935359951/p101791.png)


## Share one GPU among multiple containers

1.  Add labels to GPU-accelerated nodes.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane of the ACK console, click **Clusters**.

    3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster, or click **Applications** in the **Actions** column.

    4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Nodes**.

    5.  On the **Nodes** page, click **Manage Labels and Taints** in the upper-right corner of the page.

    6.  On the **Manage Labels and Taints** page, select the nodes to which you want to add a label and click **Add Label**.

    7.  In the **Add** dialog box, set Name to cgpu, set Value to true, and then click **OK**.

        **Note:** If a worker node is added with the cgpu=true label, the GPU resource **nvidia.com/gpu** is not exclusive to the worker node. To disable GPU sharing for the worker node, set the value of cgpu to false. This makes the GPU resource **nvidia.com/gpu** exclusive to the worker node.

2.  Install the cGPU component.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **App Catalog**.

    3.  On the App Catalog page, search for ack-cgpu and click ack-cgpu after it appears.

    4.  In the **Deploy** section on the right side of the page, select the cluster you created, select the namespace where you want to deploy ack-cgpu, and then click **Create**.

    5.  Log on to a master node and run the following command to query GPU resources.

        For more information, see [t16645.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to Kubernetes clusters by using kubectl.md).

        ```
        kubectl inspect cgpu
        ```

        Expected output:

        ```
        NAME                       IPADDRESS      GPU0(Allocated/Total)  GPU Memory(GiB)
        cn-hangzhou.192.168.2.167  192.168.2.167  0/15                   0/15
        ----------------------------------------------------------------------
        Allocated/Total GPU Memory In Cluster:
        0/15 (0%)
        ```

        **Note:** The output indicates that the GPU resources are switched from GPUs to GPU memory.

3.  Deploy workloads that share GPU resources.

    1.  Modify the YAML file that was used to deploy the sample application.

        -   Modify the number of pod replicas from 1 to 2. This allows you to deploy two pods to run the application. Before you change the number of pod replicas, the GPU is exclusive to the pod. After you change the number of pod replicas, the GPU is shared by two pods.
        -   Change the resource type from `nvidia.com/gpu` to `aliyun.com/gpu-mem`. The unit of GPU resources is changed to GB.
        ```
        apiVersion: apps/v1
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
                    aliyun.com/gpu-mem: 4   # Each pod requests 4 GB of GPU memory. Two pod replicas are configured, therefore a total of 8 GB of GPU memory is requested by the application.
        ```

    2.  Recreate workloads based on the modified configurations.

        The output indicates that the two pods are scheduled to the same GPU-accelerated node.

        ```
        kubectl inspect cgpu -d
        ```

        Expected output:

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

    3.  Run the following command to log on to two containers one after one.

        The output indicates that the GPU memory limit is 4,301 MiB, which means that each container can use at most 4,301 MiB of GPU memory.

        ```
        kubectl exec -it app-3g-v1-0 nvidia-smi
        ```

        Expected output:

        ```
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
        ```

        ```
        kubectl exec -it app-3g-v1-1 nvidia-smi
        ```

        Expected output:

        ```
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

    4.  Log on to the GPU-accelerated node to check the GPU usage.

        The output indicates that the total used GPU memory is 6,396 MiB, which is the sum of the memory that is used by the two containers. This shows the cGPU solution has isolated GPU memory usage by container. If you log on to a container and apply for more GPU resources, a memory allocation error is returned.

        1.  Run the following command to log on to a GPU-accelerated node:

            ```
            kubectl exec -it app-3g-v1-1 bash
            ```

        2.  Run the following command to query the GPU usage:

            ```
            cuda_malloc -size=1024
            ```

            Expected output:

            ```
            gpu_cuda_malloc starting...
            Detected 1 CUDA Capable device(s)
            
            Device 0: "Tesla V100-SXM2-16GB"
              CUDA Driver Version / Runtime Version          10.1 / 10.1
              Total amount of global memory:                 4301 MBytes (4509925376 bytes)
            Try to malloc 1024 MBytes memory on GPU 0
            CUDA error at cgpu_cuda_malloc.cu:119 code=2(cudaErrorMemoryAllocation) "cudaMalloc( (void**)&dev_c, malloc_size)"
            ```


You canmonitor the GPU usage of each application or node in the [ARMS console](https://arms-intl.console.aliyun.com/).

-   GPU APP: You can view the amount and percentage of GPU memory used by each application.

    ![GPU App](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7935359951/p101827.png)

-   GPU Node: You can view the memory usage of each GPU.

    ![GPU node](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7935359951/p101828.png)


## Use the managed Prometheus plug-in to monitor a shared GPU

If the amount of GPU memory requested by an application exceeds the upper limit, the GPU memory isolation module of the cGPU solution can prevent other applications from being affected.

1.  Deploy a new application that uses the shared GPU.

    The application requests 4 GB of GPU memory. However, the actual memory usage of the application is 6 GB.

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
                aliyun.com/gpu-mem: 4 # Each pod requests 4 GB of GPU memory. One pod replica is configured for the application, therefore a total of 4 GB of GPU memory is requested by the application.
    ```

2.  Run the following command to query the state of the pod.

    The pod that runs the new application remains in the **CrashLoopBackOff** state. The two existing pods are running as normal.

    ```
    kubectl get pod
    ```

    Expected output:

    ```
    NAME          READY   STATUS             RESTARTS   AGE
    app-3g-v1-0   1/1     Running            0          7h35m
    app-3g-v1-1   1/1     Running            0          7h35m
    app-6g-v1-0   0/1     CrashLoopBackOff   5          3m15s
    ```

3.  Run the following command to check errors in the container log.

    The output indicates that the cudaErrorMemoryAllocation error has occurred.

    ```
    kubectl logs app-6g-v1-0
    ```

    Expected output:

    ```
    CUDA error at cgpu_cuda_malloc.cu:119 code=2(cudaErrorMemoryAllocation) "cudaMalloc( (void**)&dev_c, malloc_size)"
    ```

4.  Use the GPU APP dashboard provided by the managed Prometheus plug-in to view the states of containers.

    The following figure shows that existing containers are not affected after the new application is deployed.

    ![Memory isolation details in the GPU APP dashboard](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8935359951/p101900.png)


