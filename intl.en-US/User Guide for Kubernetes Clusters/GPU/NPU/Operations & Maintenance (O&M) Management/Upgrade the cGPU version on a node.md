---
keyword: [upgrade cGPU, cGPU version]
---

# Upgrade the cGPU version on a node

Container Service for Kubernetes \(ACK\) supports GPU sharing. To enable GPU sharing, you must install cGPU on the nodes. This topic describes how to upgrade the cGPU version on a node by using a command-line interface \(CLI\) and the ACK console.

-   A kubectl client is connected to the cluster. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md).
-   The ack-cgpu or ack-ai-installer component is installed in the cluster. For more information, see [Install a shared GPU](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU resource scheduling/Shared GPU scheduling/Install a shared GPU.md) or [Install the ack-ai-installer component](/intl.en-US/User Guide for Kubernetes Clusters/Scheduling/Introduction to Component Installation and Functions/ack-ai-installer/Install the ack-ai-installer component.md).
-   No workload is running on the node that you want to upgrade.

## Upgrade the cGPU version on a node by using a CLI

The cgpu-installer component runs as a DaemonSet, which is used to install cGPU on nodes. To upgrade cGPU, you must change the image version of cgpu-installer to the version to which you want to upgrade.

ACK supports two image versions of cGPU: V0.8.10 and V0.8.12.

**Note:** During the upgrade process, the node where cGPU is deployed is restarted. Make sure that no workload is running on the node before you upgrade cGPU.

1.  Run the following command to modify the image version of cgpu-installer.

    ```
    kubectl  edit ds cgpu-installer -n kube-system
    ```

    In this example, the image version is changed to V0.8.10.

    ![Image version](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9712669161/p251051.png)

2.  Uninstall the earlier version of cGPU.

    1.  Log on to the node. For more information about how to log on to a node, see [Connect to a Linux instance by using password authentication](/intl.en-US/Instance/Connect to instances/Connect to an instance by using VNC/Connect to a Linux instance by using password authentication.md).

    2.  Run the following command to stop Docker:

        ```
        systemctl stop docker
        ```

    3.  Run the following command to uninstall cGPU:

        ```
        bash /usr/local/cgpu-installer/uninstall.sh
        ```

        **Note:**

        If /usr/local/cgpu-installer/uninstall.sh does not exist, run the following command to uninstall the earlier version of cGPU.

        ```
        wget http://aliacs-k8s-cn-beijing.oss-cn-beijing.aliyuncs.com/gpushare/cgpu-uninstall.sh -O /usr/local/cgpu-installer/uninstall.sh
        ```

3.  Restart the node. For more information about how to restart a node, see [Reboot the instance](/intl.en-US/Instance/Manage instances/Restart an instance.md).


**Verify the result**

After the node is restarted, log on to the node and run the following command to query the cGPU version:

```
cat /proc/cgpu_km/version
```

Expected output:

```
0.8.10
```

The output indicates that the cGPU version is upgraded to V0.8.10.

## Upgrade the cGPU version on a node by using the console

The cgpu-installer component runs as a DaemonSet, which is used to install cGPU on nodes. To upgrade cGPU, you must change the image version of cgpu-installer to the version to which you want to upgrade.

ACK supports two image versions of cGPU: V0.8.10 and V0.8.12.

1.  Run the following command to modify the image version of cgpu-installer.

    ```
    kubectl  edit ds cgpu-installer -n kube-system
    ```

    In this example, the image version is changed to V0.8.10.

    ![Image version](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9712669161/p251051.png)

2.  Remove the node whose cGPU is to be upgraded from the cluster.

    For more information, see [Remove nodes from an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node/Remove nodes from an ACK cluster.md).

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane of the ACK console, click **Clusters**.

    3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

    4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Node**.

    5.  On the Nodes page, select the node that you want to remove and click **Batch Remove**.

    6.  In the Remove Node dialog box, select **Drain the Node**.

    7.  Click **OK**.

3.  Create a node pool.

    Create a node pool and add the node that you removed to the node pool.

    For more information, see [Create a node pool](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node pool management/Create a node pool.md).

    1.  In the left-side navigation pane of the details page, choose **Nodes** \> **Node Pools**.

    2.  On the Node Pools page, click **Create Node Pool**.

    3.  In the Create Node Pool dialog box, configure the parameters.

        For more information about the parameters, see [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md). The following table describes the parameters.

        |Parameter|Description|Recommended value|
        |---------|-----------|-----------------|
        |**Quantity**|Specify the initial number of nodes in the node pool.|In this example, set Quantity to 0.|
        |**Node Label**|You can add labels to nodes in the node pool.|        -   If ack-ai-installer is installed in the cluster, set **Key** to ack.node.gpu.schedule and **Value** to cgpu.
        -   If ack-cgpu is installed in the cluster, set **Key** to cgpu and **Value** to true. |

    4.  Click **Confirm Order** to create the node pool.

4.  Add the node to the node pool.

    After the node pool is created, you must add the node that you removed in [Step 2](#step_c63_s6f_g0e) to the node pool that you created in [Step 3](#step_6nn_wov_c4r). For more information, see [Add existing ECS instances to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node/Add existing ECS instances to an ACK cluster.md).


**Verify the result**

After the node is added to the node pool, verify that the cGPU version is upgraded.

1.  Run the following command to query the pods that run cgpu-installer on the node:

    ```
    kubectl get po -l name=cgpu-installer -n kube-system -o wide
    ```

    Expected output:

    ```
    NAME                   READY   STATUS    RESTARTS   AGE    IP                NODE                     NOMINATED NODE   READINESS GATES
    cgpu-installer-kkmp6   1/1     Running   0          4d2h   192.168.XXX.XX1   cn-beijing.192.168.XXX.XX1   <none>           <none>
    cgpu-installer-**2     1/1     Running   0          4d2h   192.168.XXX.XX2   cn-beijing.192.168.XXX.XX2   <none>           <none>
    cgpu-installer-**3     1/1     Running   0          4d2h   192.168.XXX.XX3   cn-beijing.192.168.XXX.XX3   <none>           <none>
    ```

2.  Run the following command to query the pod named `cgpu-installer-kkmp6`:

    ```
    kubectl exec -ti cgpu-installer-kkmp6 -n kube-system -- bash
    ```

3.  Run the following command to query the current cGPU version:

    ```
    nsenter -t 1 -i -p -n -u -m -- cat /proc/cgpu_km/version
    ```

    Expected output:

    ```
    0.8.10
    ```

    The output indicates that the cGPU version is upgraded to V0.8.10.


