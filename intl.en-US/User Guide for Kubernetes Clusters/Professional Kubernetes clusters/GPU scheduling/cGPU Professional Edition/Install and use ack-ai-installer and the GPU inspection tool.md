---
keyword: [install cGPU, GPU inspection tool, GPU sharing]
---

# Install and use ack-ai-installer and the GPU inspection tool

Container Service for Kubernetes \(ACK\) provides GPU sharing based on cGPU. You can use cGPU to share GPU resources in predictive modeling scenarios. In addition, the NVIDIA kernel driver ensures that the GPU memory allocated to each container is isolated from one another. This topic describes how to install ack-ai-installer and the GPU inspection tool on a GPU-accelerated node. This enables GPU sharing and memory isolation.

-   The CPU policy of the node for which you want to enable GPU scheduling is not set to `static`.
-   A professional Kubernetes cluster is created. When you create the cluster, you must select instances that belong to the **heterogeneous computing** instance family, such as GPU-accelerated, FPGA-accelerated, and NPU-accelerated instances. For more information, see [Create a professional managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Create a professional managed Kubernetes cluster.md).

    **Note:** You can install ack-ai-installer in only professional Kubernetes clusters. If you want to install ack-ai-installer in dedicated Kubernetes clusters, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to be added to the whitelist.

-   [t16645.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to ACK clusters by using kubectl.md).
-   You can install ack-ai-installer on nodes that are deployed in all regions. However, only regions in the following table support GPU memory isolation. If you require GPU memory isolation, make sure that the region where your cluster is deployed is included in the following table.

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


## Limits

|Item|Supported version|
|----|-----------------|
|Kubernetes|V1.18.8 and later|
|Helm|V3.0 and later|
|NVIDIA driver|V418.87.01 and later|
|Docker|19.03.5|
|Operating System|CentOS 7.6, CentOS 7.7, Ubuntu 16.04 and 18.04, and Alibaba Cloud Linux 2.x|
|GPU|Tesla P4, Tesla P100, Tesla T4, and Tesla v100.|

## Step 1: Install ack-ai-installer

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Applications** \> **AI Project Acceleration \(Public Preview\)**.

5.  On the **Cloud-native AI Component Set** page, click **Deploy**.

6.  On the Cloud-native AI Component Set page, select **Scheduling Component \(Batch Task Scheduling, GPU Sharing, Topology-aware GPU scheduling, and NPU scheduling\)** and click **Deploy Cloud-native AI Component Set**.

    ![1](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/9018450361/p306167.png)

    **Note:** If the cloud-native AI component set is already deployed, find **ack-ai-installer** on the **Cloud-native AI Component Set** page and click **Install** in the **Actions** column.

    After the component is installed, you can find **ack-ai-installer** in the Deployed state on the **Cloud-native AI Component Set** page.


## Step 2: Enable GPU sharing and memory isolation

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster where ack-ai-installer is installed and click **Node Pools** in the **Actions** column.

4.  In the upper-right corner of the Node Pools page, click **Create Node Pool**.

5.  In the Create Node Pool dialog box, configure the node pool.

    For more information, see [Create a professional managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Create a professional managed Kubernetes cluster.md). The following list describes some of the parameters.

    -   Quantity: Specify the initial number of nodes in the node pool. If you do not want to add nodes to the node pool, set this parameter to 0.
    -   Operating System: Select an operating system for the nodes in the node pool. Supported operating systems include CentOS, Aliyun Cloud Linux 2.x, and Windows.
    -   ECS Label: You can add labels to the Elastic Compute Service \(ECS\) instances in the node pool.
    -   Custom Resource Group: You can specify the resource group to which the nodes in the node pool belong.
    -   Node Label: You can add labels to the nodes. For more information about node labels, see [Labels used by ACK to control GPUs](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/Labels used by ACK to control GPUs.md).
        -   Enable both GPU sharing and memory isolation.

            Click ![Node labels](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/3817284161/p183919.png) on the right side of **Node Label**. Set **Key** to ack.node.gpu.schedule and set **Value** to cgpu.

            **Note:** If you want to enable only GPU sharing for the node pool, set **Key** to ack.node.gpu.schedule and set **Value** to share for **Node Label**.

        -   Use the binpack algorithm to select GPUs for pods.

            Click ![Node labels](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/3817284161/p183919.png) on the right side of **Node Label**. Set **Key** to ack.node.gpu.placement and set **Value** to binpack.

6.  Click **Confirm Order**.


## Step 3: Add GPU-accelerated nodes

After the node pool is created, you can add GPU-accelerated nodes to the node pool. When you add GPU-accelerated nodes, you must select instances that belong to the **heterogeneous computing** instance family, such as GPU-accelerated, FPGA-accelerated, and NPU-accelerated instances. For more information, see [Add existing ECS instances to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node/Add existing ECS instances to an ACK cluster.md) or [Scale out a node pool](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node pool management/Manage node pools.md).

**Note:** If you have added GPU-accelerated nodes to the node pool when you created the node pool, skip this step.

## Step 4: Install and use the GPU inspection tool

1.  Configure the kubeconfig file. For more information, see [t16645.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to ACK clusters by using kubectl.md).

2.  Download kubectl-inspect-cgpu.

    -   If you use Linux, run the following command to download kubectl-inspect-cgpu:

        ```
        wget http://aliacs-k8s-cn-beijing.oss-cn-beijing.aliyuncs.com/gpushare/kubectl-inspect-cgpu-linux -O /usr/local/bin/kubectl-inspect-cgpu
        ```

    -   If you use macOS, run the following command to download kubectl-inspect-cgpu:

        ```
        wget http://aliacs-k8s-cn-beijing.oss-cn-beijing.aliyuncs.com/gpushare/kubectl-inspect-cgpu-darwin -O /usr/local/bin/kubectl-inspect-cgpu
        ```

3.  Run the following command to make kubectl-inspect-cgpu executable:

    ```
    chmod +x /usr/local/bin/kubectl-inspect-cgpu
    ```

4.  Run the following command to query the GPU usage of the cluster:

    ```
    kubectl inspect cgpu
    ```

    Expected output:

    ```
    NAME                       IPADDRESS      GPU0(Allocated/Total)  GPU Memory(GiB)
    cn-shanghai.192.168.6.104  192.168.6.104  0/15                   0/15
    ----------------------------------------------------------------------
    Allocated/Total GPU Memory In Cluster:
    0/15 (0%)
    ```


