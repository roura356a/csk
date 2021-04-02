---
keyword: [cGPU, node pool, GPU sharing, memory isolation]
---

# Use node pools to control cGPU

You can use node pools to regulate the GPU sharing and memory isolation policies of cGPU. In this topic, two labeled node pools are created to demonstrate how to use node pools to control the GPU sharing and memory isolation capabilities of cGPU.

Make sure that the following prerequisite are met:

-   [Install a shared GPU](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/Shared GPU scheduling/Install a shared GPU.md).
-   Node pools are configured.

    You can customize the names of the node pools. In this example, the node pools are named cgpu and cgpu-no-isolation.

    |Node pool name|GPU sharing|Memory isolation|Label|
    |--------------|-----------|----------------|-----|
    |cgpu|Enabled|Enabled|    -   cgpu=true
    -   cgpu.disable.isolation=false |
    |cgpu-no-isolation|Enabled|Disabled|    -   cgpu=true
    -   cgpu.disable.isolation=true |


When you use cGPU in a cluster of Container Service for Kubernetes \(ACK\), the following scenarios may occur at the same time:

-   The amount of GPU memory that can be allocated to Job A is already specified in the script. In this case, the ACK cluster needs only to enable GPU sharing for Job A. No memory isolation is required.
-   The amount of GPU memory that can be allocated to Job B is not specified in the script. In this case, the ACK cluster must enable both GPU sharing and memory isolation for Job B.

How do I configure an ACK cluster to support both scenarios?

To resolve this problem, you can use node pools to control cGPU. You must create two node pools:

-   Create a node pool that supports only GPU sharing. Do not enable memory isolation. This node pool is used to run Job A.
-   Create another node pool that supports both GPU sharing and memory isolation. This node pool is used to run Job B.

## Usage notes

When you use node pools to control cGPU, take note of the following limits:

-   When you use node pools to control cGPU, if a job is not configured with a node selector, the pods of the job may be scheduled to other node pools. This may cause job execution errors.

    **Note:** We recommend that you configure a node selector for each job.

-   When a label of a node is changed, you must restart gpushare-device-plugin on the node to make the configuration of memory isolation take effect. For example, if label cgpu.disable.isolation=false is changed to cgpu.disable.isolation=true, you must restart gpushare-device-plugin.

    The restart strategy deletes the pod of gpushare-device-plugin. Then, ACK automatically creates a new pod. To complete this task, perform the following operations:

    1.  Query the pod of gpushare-device-plugin in the ACK cluster.

        Run the following command:

        ```
        kubectl get po -n kube-system  -l name=gpushare-device-plugin-ds -o wide
        ```

        The following output is returned:

        ```
        NAME                              READY   STATUS    RESTARTS   AGE   IP              NODE                        NOMINATED NODE   READINESS GATES
        gpushare-device-plugin-ds-6r8gs   1/1     Running   0          18h   192.168.7.157   cn-shanghai.192.168.7.157   <none>           <none>
        gpushare-device-plugin-ds-pjrvn   1/1     Running   0          15h   192.168.7.158   cn-shanghai.192.168.7.158   <none>           <none>
        ```

    2.  In this example, the pod of gpushare-device-plugin on node cn-shanghai.192.168.7.157 is deleted. Then, ACK automatically creates a new pod. Run the following command:

        ```
        kubectl delete po gpushare-device-plugin-ds-6r8gs -n kube-system
        ```


## Step 1: Create node pools

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click its ID.

4.  On the Node Pools page, click **Create Node Pool**.

5.  In the **Create Node Pool** dialog box, set the parameters.

    For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md). The following list describes some of the parameters:

    -   **Quantity**: Specify the initial number of nodes in the node pool. If you do not need to create nodes in the node pool, set this parameter to 0.
    -   **Operating System**: Select an operating system for the nodes. Valid values: CentOS, AliyunLinux, and Windows.
    -   **Node Label**: You can add labels to the nodes.
    -   **ECS Label**: You can add labels to the Elastic Compute Service \(ECS\) instances.
    -   **Custom Resource Group**: You can specify the resource group to which the nodes in the node pool belong.
    In the **Node Label** section, you can add specified labels to each node pool.

    -   You must add the following labels to node pool cgpu: cgpu=true and cgpu.disable.isolation=false.
    -   You must add the following labels to node pool cgpu-no-isolation: cgpu=true and cgpu.disable.isolation=true.
    The following figure shows the labels that are added to node pool cgpu-no-isolation.

    ![Node Label](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0946876061/p178925.png)

6.  Click **Confirm Order**.

    On the Node Pools page, if the **state** of the node pool is **Initializing**, it indicates that the node pool is being created. After the node pool is created, the **state** of the node pool changes to **Active**.


**Note:** If you want to add GPU-accelerated nodes to the node pool, you can scale out the node pool. For more information, see [Scale out a node pool](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node pool management/Scale out a node pool.md).

## Step 2: Submit jobs

Submit two jobs named cgpu-test and cgpu-test-no-isolation. You must set nodeSelector in the YAML files of both jobs.

-   cgpu-test: The amount of GPU memory to be allocated to this job is not specified in the script of the job. Therefore, memory isolation is required to run this job without errors. The following YAML template is an example:

    ```
    apiVersion: apps/v1beta1
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
          nodeSelector:        # Add a node selector and select node pool cgpu.
            cgpu.disable.isolation: "false"
          containers:
          - name: cgpu-test
            image: registry.cn-shanghai.aliyuncs.com/tensorflow-samples/tensorflow-gpu-mem:10.0-runtime-centos7
            command:
              - python3
              - /app/main.py
            env:
            resources:
              limits:         # Apply for 3 GiB of GPU memory.
                aliyun.com/gpu-mem: 3
    ```

    **Note:**

    -   **nodeSelector**: Select node pool cgpu.
    -   **cgpu.disable.isolation=false**: Schedule jobs to the nodes in node pool cgpu.
    -   **aliyun.com/gpu-mem**: Specify the amount of GPU memory.
-   cgpu-test-no-isolation: The amount of memory to be allocated to this job per GPU is specified in the script of the job. Therefore, memory isolation is not required. The following YAML template is an example:

    ```
    apiVersion: apps/v1beta1
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
          nodeSelector:      # Add a node selector and select node pool cgpu-no-isolation.
            cgpu.disable.isolation: "true"      
          containers:
          - name: cgpu-test-no-isolation
            image: cheyang/gpu-player:v2
            resources:
              limits:    # Apply for 3 GiB of GPU memory.
                aliyun.com/gpu-mem: 3
    ```

    **Note:**

    -   **nodeSelector**: Select node pool cgpu-no-isolation.
    -   **cgpu.disable.isolation=true**: Schedule jobs to the nodes in node pool cgpu-no-isolation.
    -   **aliyun.com/gpu-mem**: Specify the amount of GPU memory.

## Step 3: View the job execution results

1.  After the two jobs are submitted, run the following command to query the status of the jobs:

    ```
    kubectl get po
    ```

    The following output is returned:

    ```
    NAME                       READY   STATUS    RESTARTS   AGE
    cgpu-test-0                1/1     Running   0          5m55s
    cgpu-test-no-isolation-0   1/1     Running   0          6m42s
    ```

2.  Run the `nvidia-smi` command in pod cgpu-test-0 \(requires GPU memory isolation\) to query the amount of GPU memory that can be used by the containers in the pod.

    Run the following command:

    ```
    kubectl exec cgpu-test-0 nvidia-smi
    ```

    The following output is returned:

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

    The preceding output shows that 3,226 MiB of GPU memory can be used by the containers. The total GPU memory is 16 GiB. This means that GPU memory isolation is enabled.

3.  Run the `nvidia-smi` command in pod cgpu-test-no-isolation-0 \(does not require GPU memory isolation\) to query the amount of GPU memory that can be used by the containers in the pod.

    Run the following command:

    ```
    kubectl exec cgpu-test-no-isolation-0 nvidia-smi
    ```

    The following output is returned:

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

    The preceding output shows that 16,130 MiB of GPU memory can be used by the containers. The total GPU memory is 16 GiB. This means that GPU memory isolation is disabled. In this case, you must add the following environment variables to apply for a required amount of GPU memory that can be used by the containers.

    Run the following command:

    ```
    kubectl exec cgpu-test-no-isolation-0 env | grep ALIYUN
    ```

    The following output is returned:

    ```
    ALIYUN_COM_GPU_MEM_CONTAINER=3    # Specify the amount of memory that can be allocated from a GPU to the containers. In this example, 3 GiB is specified.
    ALIYUN_COM_GPU_MEM_DEV=15      # The total memory of the GPU.
    ...
    ```

4.  After you run the `nvidia-smi` command, compare the results returned from pod cgpu-test-no-isolation-0 and pod cgpu-test-0.

    Pod cgpu-test-no-isolation-0 is returned with the total amount of GPU memory and pod cgpu-test-0 is returned with the amount of GPU memory for which you have applied. This indicates that you can use node pools to control cGPU for GPU sharing and memory isolation.


