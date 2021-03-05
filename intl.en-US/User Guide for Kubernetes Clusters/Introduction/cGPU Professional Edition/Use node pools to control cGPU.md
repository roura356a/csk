---
keyword: [node pool, memory isolation, specific label]
---

# Use node pools to control cGPU

You can use node pools to regulate the graphics processing unit \(GPU\) sharing and memory isolation policies of cGPU in professional managed Kubernetes clusters. This topic describes how to use node pools to control cGPU.

-   Helm 3.0.0 or later is used.
-   A professional managed Kubernetes cluster is created.

    When you create the cluster, set the Kubernetes version and instance types based on the following description. For more information about other cluster parameters, see [Create a professional managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Introduction/Create a professional managed Kubernetes cluster.md).

    **Note:** You can install cGPU in only professional managed Kubernetes clusters. If you want to install cGPU in dedicated Kubernetes clusters, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to add your account to the whitelist.

    -   The Kubernetes version must be V1.18.8 or later.
    -   You must select the instance types for **heterogeneous computing**, including GPU-accelerated, FPGA-accelerated, and NPU-accelerated instances.
-   Only regions in the following table support GPU memory isolation. If you require GPU memory isolation, make sure that the region where your cluster is deployed is included in the following table.

    |Region|Region ID|
    |------|---------|
    |China \(Beijing\)|cn-beijing|
    |China \(Shanghai\)|cn-shanghai|
    |China \(Hangzhou\)|cn-hangzhou|
    |China \(Zhangjiakou\)|cn-zhangjiakou|
    |China \(Shenzhen\)|cn-shenzhen|
    |China \(Chengdu\)|cn-chengdu|
    |China \(Heyuan\)|cn-heyuan|
    |China \(Hong Kong\)|cn-hongkong|
    |Indonesia \(Jakarta\)|ap-southeast-5|
    |Singapore \(Singapore\)|ap-southeast-1|
    |US \(Virginia\)|us-east-1|
    |US \(Silicon Valley\)|us-west-1|

-   Node pools are created with names and labels that are set as required to implement GPU sharing and memory isolation.

    You can customize the names of the node pools. In this example, the node pools are named cgpu and cgpu-no-isolation. For more information, see [Description of labels for GPU-accelerated nodes](/intl.en-US/User Guide for Kubernetes Clusters/Introduction/Description of labels for GPU-accelerated nodes.md).

    |Node pool name|GPU sharing|Memory isolation|Node Label|
    |--------------|-----------|----------------|----------|
    |cgpu-no-isolation|Supported|Not supported|    -   ack.node.gpu.schedule=share
    -   ack.node.gpu.placement=binpack |
    |cgpu|Supported|Supported|    -   ack.node.gpu.schedule=cgpu
    -   ack.node.gpu.placement=binpack |


## Scenarios

When you use cGPU in a cluster of Container Service for Kubernetes \(ACK\), the following scenarios may exist at the same time:

-   The amount of GPU memory that can be allocated to Job A is already specified in the script. In this case, the ACK cluster only needs to enable GPU sharing for Job A. No memory isolation is required.
-   The amount of GPU memory that can be allocated to Job B is not specified in the script. In this case, the ACK cluster must enable both GPU sharing and memory isolation for Job B.

To support both scenarios as the same time in an ACK cluster, you can use node pools to control cGPU. You only need to create two node pools in the cluster:

-   Create a node pool that supports only GPU sharing. Do not enable memory isolation. This node pool is used to run Job A.
-   Create another node pool that supports both GPU sharing and memory isolation. This node pool is used to run Job B.

## Considerations

When you use node pools to control cGPU, take note of the following limits:

-   We recommend that you configure a node selector for each job.

    When you control cGPU based on node pools, if a job is not configured with a node selector, the pods of the job may be scheduled to other node pools. For example, job pods that do not require memory isolation may be scheduled to nodes that support memory isolation.

-   To disable memory isolation for a node, uninstall the kernel module of cGPU on the node and restart the instance.
    -   If label ack.node.gpu.schedule=cgpu is changed to ack.node.gpu.schedule=share on a node, memory isolation is not disabled for the node. You must uninstall the kernel module of cGPU on the node and restart the instance. For more information about how to uninstall the kernel module of cGPU on a node, see [Use cGPU to isolate GPU resources]().
    -   If label ack.node.gpu.schedule=share is changed to ack.node.gpu.schedule=cgpu on a node, memory isolation is enabled for the node.

## Step 1: Install ack-ai-installer

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **App Catalog**.

3.  In the upper-right corner of the App Catalog page, enter ack-ai-installer into the search bar and click the search icon. Find and click **ack-ai-installer**.

4.  On the App Catalog - ack-ai-installer page, select a cluster in the Deploy section to deploy ack-ai-installer and click **Create**.

    After ack-ai-installer is installed, you are redirected to the details page of ack-ai-installer. You can view the plug-ins of ack-ai-installer.


## Step 2: Create node pools

Create a node pool that supports both GPU sharing and memory isolation. Create another node pool that supports only GPU sharing.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster where cGPU is deployed and click **Node Pools** in the **Actions** column.

4.  In the upper-right corner of the Node Pools page, click **Create Node Pool**.

5.  In the Create Node Pool dialog box, configure the node pool.

    For more information, see [Create a professional managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Introduction/Create a professional managed Kubernetes cluster.md). The following list describes some of the parameters:

    -   Name: Set **Name** to cgpu.
    -   Quantity: Specify the initial number of nodes in the node pool. If you do not need to create nodes in the node pool, set this parameter to 0.
    -   Operating System: Select an operating system for the nodes. Supported operating systems are CentOS, AliyunLinux, and Windows.
    -   ECS Label: You can add labels to the ECS instances.
    -   Custom Resource Group: You can specify the resource group to which the nodes in the node pool belong.
    -   Node Label: You can add labels to the nodes.
        -   Enable both GPU sharing and memory isolation.

            Click ![Node Label](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3817284161/p183919.png) on the right side of **Node Label**. Set **Key** to ack.node.gpu.schedule and set **Value** to cgpu.

        -   Use the binpack algorithm to allocate GPUs to pods.

            Click ![Node Label](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3817284161/p183919.png) on the right side of **Node Label**. Set **Key** to ack.node.gpu.placement and set **Value** to binpack.

6.  Repeat Step [4](#step_ljg_wgy_9f0) and Step [5](#step_2r4_6w1_1g2) to create node pool cgpu-no-isolation.

    When you create node pool cgpu-no-isolation, set the name to cgpu-no-isolation and add labels ack.node.gpu.schedule=share and ack.node.gpu.placement=binpack to the nodes in the node pool.


## Step 3: Add GPU-accelerated nodes

After the node pool is created, you can add GPU-accelerated nodes to the node pool. When you add GPU-accelerated nodes, you must select the instance types for **heterogeneous computing**, including GPU-accelerated, FPGA-accelerated, and NPU-accelerated instances. For more information, see [Add existing ECS instances to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Add existing ECS instances to an ACK cluster.md) or [Scale out a node pool](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node pool management/Scale out a node pool.md).

**Note:** If you have already added GPU-accelerated nodes to the node pool when you create the node pool, skip this step.

## Step 4: Submit jobs

To check whether GPU sharing and memory isolation are enabled as required for the node pools, submit two jobs:

-   cgpu-test: The amount of GPU memory to be allocated to this job is not specified in the script of the job. Therefore, memory isolation is required to run this job without errors.
-   cgpu-test-no-isolation: The amount of memory to be allocated to this job per GPU is specified in the script of the job. Therefore, memory isolation is not required.

**Submit job cgpu-test-no-isolation**

1.  Create a file named cgpu-test-no-isolation.yaml.

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
      selector: # define how the deployment finds the pods it manages
        matchLabels:
          app: cgpu-test-no-isolation
    
      template: # define the pods specifications
        metadata:
          labels:
            app: cgpu-test-no-isolation
    
        spec:
          nodeSelector:
            # Add a node selector to schedule the job to matched nodes.
            # Node pool cgpu-no-isolation consists of nodes that are labeled with ack.node.gpu.schedule=share.
            ack.node.gpu.schedule: "share"
          containers:
          - name: cgpu-test-no-isolation
            image: cheyang/gpu-player:v2
            resources:
              limits:
                # Request 3 GiB of GPU memory.
                aliyun.com/gpu-mem: 3
    ```

    -   aliyun.com/gpu-mem: Specify the amount of GPU memory.
    -   nodeSelector: Select node pool cgpu-no-isolation.
2.  Submit job cgpu-test-no-isolation

    ```
    kubectl apply -f cgpu-test-no-isolation.yaml 
    ```


**Submit job cgpu-test**

1.  Create a file named cgpu-test.yaml.

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
      selector: # define how the deployment finds the pods it manages
        matchLabels:
          app: cgpu-test
    
      template: # define the pods specifications
        metadata:
          labels:
            app: cgpu-test
    
        spec:
          nodeSelector:
            # Add a node selector and select node pool cgpu.
            ack.node.gpu.schedule: "cgpu"
          containers:
          - name: cgpu-test
            image: registry.cn-shanghai.aliyuncs.com/tensorflow-samples/tensorflow-gpu-mem:10.0-runtime-centos7
            command:
              - python3
              - /app/main.py
            env:
            resources:
              limits:
                # Request 3 GiB of GPU memory.
                aliyun.com/gpu-mem: 3
    ```

    -   aliyun.com/gpu-mem: Specify the amount of GPU memory.
    -   nodeSelector: Select node pool cgpu.
2.  Submit job cgpu-test

    ```
    kubectl apply -f cgpu-test.yaml 
    ```


## Step 5: Verify the job execution results

If memory isolation is enabled, the amount of GPU memory that a container can use equals the amount that is allocated from the GPU. If memory isolation is disabled, the amount of GPU memory that a container can use equals the total memory of the GPU. Run the following command to query the amount of GPU memory that can be used by the job containers:

-   Run the following command to query the job pods:

    ```
    kubectl get po
    ```

    Expected output:

    ```
    NAME                       READY   STATUS    RESTARTS   AGE
    cgpu-test-0                1/1     Running   0          5m55s
    cgpu-test-no-isolation-0   1/1     Running   0          6m42s
    ```

-   Run the following command to query the amount of GPU memory that can be used by container cgpu-test-0:

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

    The preceding output shows that 3,226 MiB of GPU memory can be used by container cgpu-test-0. The total GPU memory is 16 GiB. This means that GPU memory isolation is enabled.

-   Run the following command to query the amount of GPU memory that can be used by container cgpu-test-no-isolation-0:

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

    The preceding output shows that 16,130 MiB of GPU memory can be used by container cgpu-test-no-isolation-0. The total GPU memory is 16 GiB. This means that GPU memory isolation is disabled.

    Compare the results returned from container cgpu-test-no-isolation-0 and container cgpu-test-0, you can find that container cgpu-test-no-isolation-0 is allocated with the total amount of GPU memory and container cgpu-test-0 is allocated with only the applied amount of GPU memory. This means that you succeed to use node pools to control cGPU for GPU sharing and memory isolation.


