---
keyword: Upgrade the NVIDIA driver
---

# Use a node pool to upgrade the NVIDIA driver for a node

If your CUDA Toolkit requires a higher version of the NVIDIA driver, you must upgrade the NVIDIA driver. To upgrade the NVIDIA driver for a node, you can remove the node from the cluster and add the node back to the cluster. When the node is added to the cluster, the operating system of the node is reinstalled. This allows you to install a specific version of the NVIDIA driver. This topic describes how to use a node pool to upgrade the NVIDIA driver for a node.

Container Service for Kubernetes \(ACK\) does not allow you to upgrade the NVIDIA driver for a node without removing the node from the cluster. Your existing node pool may contain different nodes. Therefore, you cannot upgrade the NVIDIA driver for the entire node pool.

## Benefits

This solution allows you to manage the NVIDIA drivers of different nodes in batches. The following two scenarios are covered:

-   You manage cluster nodes in two groups: A and B. You want to upgrade the NVIDIA driver of Group A to version 418.181.07 and the NVIDIA driver of Group B to version 450.102.0. In this case, you can add nodes in Group A to Node Pool A and nodes in Group B to Node Pool B.
-   Node Pool A consists of all nodes whose NVIDIA drivers are to be upgraded to version 418.181.07. If you want to schedule a task to a node that runs the NVIDIA driver of version 418.181.07, you need only to specify the **selector** of the task to the label of Node Pool A.

**Note:**

-   To upgrade the NVIDIA driver for a node, you must remove the node from the cluster and add the node back to the cluster. When the node is added to the cluster, the operating system of the node is reinstalled and the NVIDIA driver of the specified version is installed. Before you perform the upgrade, make sure that no task is running on the node and critical data is backed up.
-   To lower security risks, we recommend that you first upgrade the NVIDIA driver for one node. If no error occurs during this process, you can then perform the upgrade on multiple nodes.

## Step 1: Determine the NVIDIA driver version

You must consider which CUDA Toolkit versions are compatible with the NVIDIA driver version that you want to use. The following figure displays a list of CUDA Toolkit versions and the compatible NVIDIA driver versions.

**Note:** By default, ACK clusters are installed with the NVIDIA driver of version 418.87.01.

![1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5146487161/p245597.png)

For more information, see [CUDA compatibility](https://docs.nvidia.com/deploy/cuda-compatibility/index.html).

## Step 2: Remove the node from the cluster

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Node**.

5.  Select the node that you want to upgrade and click **Batch Remove**.

6.  In the **Remove Node** dialog box, select **Drain the Node** and click **OK**.


## Step 3: Create a node pool and specify the NVIDIA driver version

**Method 1: Select an NVIDIA driver version provided by ACK**

**Note:** This method is simple. You need only to add the `ack.aliyun.com/nvidia-driver-version=<Driver Version>` label when you create a node pool and then add the node that you removed to the node pool.

The following example shows how to set the NVIDIA driver version to 418.181.07:

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Node Pools**.

5.  Click **Create Node Pool** in the upper-right corner.

6.  In the **Create Node Pool** dialog box, configure the parameters based on your requirements. For more information about the parameters, see [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md). The following table describes the parameters.

    |Parameter|Description|
    |---------|-----------|
    |**VSwitch**|These parameters are used only when new nodes are added to the node pool. In this example, an existing node is added to the node pool. Therefore, these parameters are not used.|
    |**Instance Type**|
    |**Operating System**|The operating system of the node after the node is added to the cluster.|
    |**Quantity**|Set the value to 0. Otherwise, ACK creates new instances. ![ACK2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5146487161/p245634.png) |

    1.  Click **Show Advanced Options**.

    2.  In the **Node Label** section, click ![1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5146487161/p245638.png), set **Key** to `ack.aliyun.com/nvidia-driver-version`, and set **Value** to `418.181.07`.

        ACK provides the following NVIDIA driver versions:

        -   418.181.07
        -   450.102.04
    3.  Click **Confirm Order**.


**Method 2: Use a custom NVIDIA driver version**

**Custom driver version**

The following example shows how to upload the NVIDIA-Linux-x86\_64-460.32.03.run driver to specify a custom driver version. You can download NVIDIA drivers from the [NVIDIA official website](https://www.nvidia.com/Download/index.aspx?lang=en-us).

**Note:** The NVIDIA-Linux-x86\_64-460.32.03.run file must be stored in the root directory of the Object Storage Service \(OSS\) bucket.

1.  Create a bucket in the OSS console. For more information, see [Create buckets](/intl.en-US/Console User Guide/Manage buckets/Create buckets.md).

2.  Upload the NVIDIA-Linux-x86\_64-460.32.03.run file to the bucket. For more information, see [Upload objects](/intl.en-US/Console User Guide/Upload, download, and manage objects/Upload objects.md).

3.  In the OSS console, select the NVIDIA driver file and click **View Details** in the **Actions** column.

4.  In the **Details** panel, click **HTTPS**![gpu-1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5146487161/p246955.png) to disable HTTPS configuration.

    **Note:** ACK downloads the driver file by using its URL, which adopts the HTTP protocol. By default, OSS uses the HTTPS protocol. You must click **HTTPS** to disable HTTPS configuration.

5.  Check the URL of the driver file. Take note of the URL. The URL consists of the following parts: endpoint and runfile. For example, you can divide the following URL into two parts: `[http://nvidia-XXX-XXX-cn-beijing.aliyuncs.com/NVIDIA-Linux-x86\_64-460.32.03.run](http://nvidia-XXX-XXX-cn-beijing.aliyuncs.com/NVIDIA-Linux-x86_64-460.32.03.run)`.

    -   endpoint: nvidia-XXX-XXX-cn-beijing.aliyuncs.com
    -   runfile: NVIDIA-Linux-x86\_64-460.32.03.run

**Create a node pool with the custom driver version**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Node Pools**.

5.  Click **Create Node Pool** in the upper-right corner.

6.  In the **Create Node Pool** dialog box, configure the parameters based on your requirements. For more information about the parameters, see [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md). The following table describes the parameters.

    |Parameter|Description|
    |---------|-----------|
    |**VSwitch**|These parameters are used only when new nodes are added to the node pool. In this example, an existing node is added to the node pool. Therefore, these parameters are not used.|
    |**Instance Type**|
    |**Operating System**|The operating system of the node after the node is added to the cluster.|
    |**Quantity**|Set the value to 0. Otherwise, ACK creates new instances. ![ACK2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5146487161/p245634.png) |

    1.  Click **Show Advanced Options**.

    2.  In the **Node Label** section, click ![1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5146487161/p245638.png) twice to add the following two labels:

        -   For the first label, set **Key** to `ack.aliyun.com/nvidia-driver-oss-endpoint` and **Value** to `nvidia-XXX-XXX-cn-beijing.aliyuncs.com`.
        -   For the second label, set **Key** to `ack.aliyun.com/nvidia-driver-runfile` and **Value** to `NVIDIA-Linux-x86_64-460.32.03.run`.
    3.  Click **Confirm Order**.


## Step 4: Add the node to the node pool

After the node pool is created, add the node that you removed to the node pool. For more information, see [Add existing ECS instances to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node/Add existing ECS instances to an ACK cluster.md).

## Step 5: Check whether the NVIDIA driver version is upgraded

1.  In the ACK console, select the cluster to which the node belongs and click **More** \> **Open Cloud Shell** in the **Actions** column.

2.  Run the following command to query pods that have the `component: nvidia-device-plugin` label:

    ```
    kubectl get po -n kube-system -l component=nvidia-device-plugin -o wide
    ```

    Expected output:

    ```
    NAME                                            READY   STATUS    RESTARTS   AGE   IP              NODE                       NOMINATED NODE   READINESS GATES
    nvidia-device-plugin-cn-beijing.192.168.1.127   1/1     Running   0          6d    192.168.1.127   cn-beijing.192.168.1.127   <none>           <none>
    nvidia-device-plugin-cn-beijing.192.168.1.128   1/1     Running   0          17m   192.168.1.128   cn-beijing.192.168.1.128   <none>           <none>
    nvidia-device-plugin-cn-beijing.192.168.8.12    1/1     Running   0          9d    192.168.8.12    cn-beijing.192.168.8.12    <none>           <none>
    nvidia-device-plugin-cn-beijing.192.168.8.13    1/1     Running   0          9d    192.168.8.13    cn-beijing.192.168.8.13    <none>           <none>
    nvidia-device-plugin-cn-beijing.192.168.8.14    1/1     Running   0          9d    192.168.8.14    cn-beijing.192.168.8.14    <none>           <none>
    ```

    You can refer to the Node column to find the node that is newly added to the cluster. The name of the pod that runs on the node is nvidia-device-plugin-cn-beijing.192.168.1.128.

3.  Run the following command to query the NVIDIA driver version of the node:

    ```
    kubectl exec -ti nvidia-device-plugin-cn-beijing.192.168.1.128 -n kube-system -- nvidia-smi 
    ```

    Expected output:

    ```
    Sun Feb  7 04:09:01 2021       
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 418.181.07   Driver Version: 418.181.07   CUDA Version: N/A      |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |===============================+======================+======================|
    |   0  Tesla V100-SXM2...  On   | 00000000:00:07.0 Off |                    0 |
    | N/A   27C    P0    40W / 300W |      0MiB / 16130MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    |   1  Tesla V100-SXM2...  On   | 00000000:00:08.0 Off |                    0 |
    | N/A   27C    P0    40W / 300W |      0MiB / 16130MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    |   2  Tesla V100-SXM2...  On   | 00000000:00:09.0 Off |                    0 |
    | N/A   31C    P0    39W / 300W |      0MiB / 16130MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    |   3  Tesla V100-SXM2...  On   | 00000000:00:0A.0 Off |                    0 |
    | N/A   27C    P0    41W / 300W |      0MiB / 16130MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
                                                                                   
    +-----------------------------------------------------------------------------+
    | Processes:                                                       GPU Memory |
    |  GPU       PID   Type   Process name                             Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    ```

    The output shows that the NVIDIA driver version is 418.181.07. This indicates that the NVIDIA driver is upgraded.


**Related topics**  


[Use a node pool to create a node with a custom NVIDIA driver version]()

