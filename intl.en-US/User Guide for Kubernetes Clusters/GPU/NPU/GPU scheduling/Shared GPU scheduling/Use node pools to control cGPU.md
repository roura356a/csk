---
keyword: [cGPU, node pool, GPU sharing, memory isolation]
---

# Use node pools to control cGPU

Container Service for Kubernetes \(ACK\) allows you to use node pools to control cGPU. This way, you can create more flexible GPU sharing and memory isolation policies. In this topic, two labeled node pools are created to demonstrate how to use node pools to control the GPU sharing and memory isolation capabilities of cGPU.

Before you start, make sure that you have performed the following operations:

-   [Install the cGPU component](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/Shared GPU scheduling/Install the cGPU component.md).

    **Note:**

    -   Only dedicated Kubernetes clusters with GPU-accelerated nodes support the component described in this topic. Managed Kubernetes clusters with GPU-accelerated nodes do not support the component described in this topic.
    -   If you want to install the cGPU component in professional Kubernetes clusters, see [Install and use ack-ai-installer and the GPU scheduling inspection tool](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/GPU scheduling/cGPU Professional Edition/Install and use ack-ai-installer and the GPU scheduling inspection tool.md).
    -   If you want to use node pools to control cGPU in professional Kubernetes clusters, see [Use node pools to control cGPU](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/GPU scheduling/cGPU Professional Edition/Use node pools to control cGPU.md).
-   Node pools are configured.

    You can customize the names of the node pools. In this example, two node pools named cgpu and cgpu-no-isolation are used.

    |Node pool name|GPU sharing|Memory isolation|Label|
    |--------------|-----------|----------------|-----|
    |cgpu|Enabled|Enabled|    -   cgpu=true
    -   cgpu.disable.isolation=false |
    |cgpu-no-isolation|Enabled|Disabled|    -   cgpu=true
    -   cgpu.disable.isolation=true |


When you use cGPU in an ACK cluster, you may come across the following scenarios:

-   The amount of GPU memory that can be allocated to Job A is already specified in the script. In this case, the ACK cluster needs only to enable GPU sharing for Job A. Memory isolation is not required.
-   The amount of GPU memory that can be allocated to Job B is not specified in the script. In this case, the ACK cluster must enable both GPU sharing and memory isolation for Job B.

How do I configure an ACK cluster to support both scenarios?

To resolve this problem, you can use node pools to control cGPU. You need only to create two node pools:

-   Create a node pool that supports only GPU sharing. This node pool is used to run Job A.
-   Create another node pool that supports both GPU sharing and memory isolation. This node pool is used to run Job B.

## Precautions

When you use node pools to control cGPU, take note of the following limits:

-   When you use node pools to control cGPU, if a job is not configured with a node selector, the pods of the job may be scheduled to other node pools. This may cause job execution errors.

    **Note:** We recommend that you configure a node selector for each job.

-   When the label of a node is changed, for example, the node label is changed from cgpu.disable.isolation=false to cgpu.disable.isolation=true, you must restart the pod of gpushare-device-plugin on the node for the configuration to take effect.

    To do this, you must delete the pod of gpushare-device-plugin on the node. Then, ACK automatically creates a new pod. You can perform the following operations:

    1.  Run the following command to query the pods of gpushare-device-plugin in the ACK cluster:

        ```
        kubectl get po -n kube-system  -l name=gpushare-device-plugin-ds -o wide
        ```

        Expected output:

        ```
        NAME                              READY   STATUS    RESTARTS   AGE   IP              NODE                        NOMINATED NODE   READINESS GATES
        gpushare-device-plugin-ds-6r8gs   1/1     Running   0          18h   192.168.7.157   cn-shanghai.192.168.7.157   <none>           <none>
        gpushare-device-plugin-ds-pjrvn   1/1     Running   0          15h   192.168.7.158   cn-shanghai.192.168.7.158   <none>           <none>
        ```

    2.  In this example, node cn-shanghai.192.168.7.157 is used. Run the following command to delete the pod of gpushare-device-plugin on this node:

        ```
        kubectl delete po gpushare-device-plugin-ds-6r8gs -n kube-system
        ```


## Step 1: Create node pools

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Node Pools**.

5.  In the upper-right corner of the Node Pools page, click **Create Node Pool**.

    In the upper-right corner of the Node Pools page, you can also click **Create Managed Node Pool** to create a managed node pool, or click **Configure Auto Scaling** to create an auto-scaling node pool.

6.  In the **Create Node Pool** dialog box, set the parameters.

    For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md). The following list describes some of the parameters:

    -   **Quantity**: Specify the initial number of nodes in the node pool. If you do not want to create nodes in the node pool, set this parameter to 0.
    -   **Operating System**: Select the operating system of the nodes. CentOS 7.x and Alibaba Cloud Linux 2.x are supported.
    -   **Node Label**: You can add labels to the nodes.
    -   **ECS Label**: You can add labels to the Elastic Compute Service \(ECS\) instances.
    -   **Custom Resource Group**: You can specify the resource group to which the nodes in the node pool belong.
    In the **Node Label** section, you can add specified labels to the nodes in the node pool.

    -   You must add the following labels to the nodes in the cgpu node pool: cgpu=true and cgpu.disable.isolation=false.
    -   You must add the following labels to the nodes in the cgpu-no-isolation node pool: cgpu=true and cgpu.disable.isolation=true.
    The following figure shows the labels that are added to the nodes in the cgpu-no-isolation node pool.

    ![Node labels](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/0946876061/p178925.png)

7.  Click **Confirm Order**.

    On the Node Pools page, check the **Status** column of the node pool. If the node pool is in the **Initializing** state, it indicates that the node pool is being created. After the node pool is created, the **Status** of the node pool changes to **Active**.


**Note:** If you want to add GPU-accelerated nodes to the node pool, you can scale out the node pool. For more information, see [Scale out a node pool](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node pool management/Manage node pools.md).

## Step 2: Submit jobs

Submit two jobs named cgpu-test and cgpu-test-no-isolation. You must set nodeSelector in the YAML files of both jobs.

-   cgpu-test: The amount of GPU memory to be allocated to this job is not specified in the script of the job. Therefore, memory isolation is required for running this job. The following YAML template is an example:

    ```
    apiVersion: apps/v1
    kind: StatefulSet
    
    metadata:
      name: cgpu-test
      labels:
        app: cgpu-test
    
    spec:
      replicas: 1
      serviceName: "cgpu-test"
      podManagementPolicy: "Parallel"
      selector:       # define how the deployment finds the pods it manages.
        matchLabels:
          app: cgpu-test
    
      template:       # define the pods specifications.
        metadata:
          labels:
            app: cgpu-test
    
        spec:
          nodeSelector:        # Add a node selector to select the cgpu node pool. 
            cgpu.disable.isolation: "false"
          containers:
          - name: cgpu-test
            image: registry.cn-shanghai.aliyuncs.com/tensorflow-samples/tensorflow-gpu-mem:10.0-runtime-centos7
            command:
              - python3
              - /app/main.py
            env:
            resources:
              limits:         # Request 3 GiB of GPU memory. 
                aliyun.com/gpu-mem: 3
    ```

    **Note:**

    -   **nodeSelector**: selects the cgpu node pool.
    -   **cgpu.disable.isolation=false**: schedules the job to the nodes in the cgpu node pool.
    -   **aliyun.com/gpu-mem**: specifies the amount of GPU memory.
-   cgpu-test-no-isolation: The amount of memory to be allocated to this job per GPU is specified in the script of the job. Therefore, memory isolation is not required for running this job. The following YAML template is an example:

    ```
    apiVersion: apps/v1
    kind: StatefulSet
    
    metadata:
      name: cgpu-test-no-isolation
      labels:
        app: cgpu-test-no-isolation
    
    spec:
      replicas: 1
      serviceName: "cgpu-test-no-isolation"
      podManagementPolicy: "Parallel"
      selector:          # define how the deployment finds the pods it manages
        matchLabels:
          app: cgpu-test-no-isolation
    
      template:          # define the pods specifications
        metadata:
          labels:
            app: cgpu-test-no-isolation
    
        spec:
          nodeSelector:      # Add a node selector to select the cgpu-no-isolation node pool. 
            cgpu.disable.isolation: "true"      
          containers:
          - name: cgpu-test-no-isolation
            image: cheyang/gpu-player:v2
            resources:
              limits:    # Request 3 GiB of GPU memory. 
                aliyun.com/gpu-mem: 3
    ```

    **Note:**

    -   **nodeSelector**: selects the cgpu-no-isolation node pool.
    -   **cgpu.disable.isolation=true**: schedules the job to the nodes in the cgpu-no-isolation node pool.
    -   **aliyun.com/gpu-mem**: specifies the amount of GPU memory.

## Step 3: View the job execution results

1.  Run the following command to query the states of the jobs:

    ```
    kubectl get po
    ```

    Expected output:

    ```
    NAME                       READY   STATUS    RESTARTS   AGE
    cgpu-test-0                1/1     Running   0          5m55s
    cgpu-test-no-isolation-0   1/1     Running   0          6m42s
    ```

2.  Run the `nvidia-smi` command in pod cgpu-test-0 \(requires GPU memory isolation\) to query the amount of GPU memory that can be used by the containers in the pod.

    ```
    kubectl exec cgpu-test-0 nvidia-smi
    ```

    Expected output:

    ```
    Mon Nov  2 11:33:10 2020
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 418.87.01    Driver Version: 418.87.01    CUDA Version: 10.1     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |===============================+======================+======================|
    |   0  Tesla V100-SXM2...  On   | 00000000:00:07.0 Off |                    0 |
    | N/A   34C    P0    54W / 300W |   3039MiB /  3226MiB |      1%      Default |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                       GPU Memory |
    |  GPU       PID   Type   Process name                             Usage      |
    |=============================================================================|
    +-----------------------------------------------------------------------------+
    ```

    The preceding output shows that 3,226 MiB of GPU memory can be used by the containers. The total GPU memory is 16 GiB. This indicates that GPU memory isolation is enabled.

3.  Run the `nvidia-smi` command in pod cgpu-test-no-isolation-0 \(does not require GPU memory isolation\) to query the amount of GPU memory that can be used by the containers in the pod.

    ```
    kubectl exec cgpu-test-no-isolation-0 nvidia-smi
    ```

    Expected output:

    ```
    Mon Nov  2 11:39:59 2020
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 418.87.01    Driver Version: 418.87.01    CUDA Version: 10.1     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |===============================+======================+======================|
    |   0  Tesla V100-SXM2...  On   | 00000000:00:07.0 Off |                    0 |
    | N/A   37C    P0    56W / 300W |   1929MiB / 16130MiB |      1%      Default |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                       GPU Memory |
    |  GPU       PID   Type   Process name                             Usage      |
    |=============================================================================|
    +-----------------------------------------------------------------------------+
    ```

    The preceding output shows that 16,130 MiB of GPU memory can be discovered by the containers. The total GPU memory is 16 GiB. This indicates that GPU memory isolation is disabled. In this case, you must use the following environment variable to query the amount of GPU memory that can be used by the containers. Run the following command to query the amount of memory that can be used by the containers:

    ```
    kubectl exec cgpu-test-no-isolation-0 env | grep ALIYUN
    ```

    Expected output:

    ```
    ALIYUN_COM_GPU_MEM_CONTAINER=3    # The amount of GPU memory that can be used by the containers. In this example, 3 GiB is specified. 
    ALIYUN_COM_GPU_MEM_DEV=15      # The total memory of the GPU. 
    ...
    ```

4.  After you run the `nvidia-smi` command, compare the results returned from pod cgpu-test-no-isolation-0 and pod cgpu-test-0.

    The result of pod cgpu-test-no-isolation-0 shows the total amount of GPU memory and the result of pod cgpu-test-0 shows only the amount of GPU memory that you have requested. This indicates that you can use node pools to control cGPU for GPU sharing and memory isolation.


