---
keyword: [ack-cgpu, install the GPU isolation module, kubectl-inspect-cgpu]
---

# Install ack-cgpu

Container Service for Kubernetes \(ACK\) provides GPU sharing based on cGPU. You can use cGPU to share one GPU in model inference scenarios. In addition, the NVIDIA kernel driver ensures that the GPU memory allocated to each container is isolated from each other. This topic describes how to install the resource isolation module and an inspection tool on a GPU-accelerated node. This enables GPU scheduling and memory isolation.

-   The CPU policy of the node for which you want to enable GPU scheduling is not set to `static`.
-   [An ACK cluster with GPU-accelerated nodes is created.](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/Use GPU scheduling for ACK clusters.md)

**Note:**

-   Only dedicated Kubernetes clusters with GPU-accelerated nodes support the ack-cgpu component. Managed Kubernetes clusters with GPU-accelerated nodes do not support the ack-cgpu component.
-   If you want to install ack-cgpu in professional Kubernetes clusters, see [Install and use ack-ai-installer and the GPU scheduling inspection tool](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/GPU scheduling/cGPU Professional Edition/Install and use ack-ai-installer and the GPU scheduling inspection tool.md).
-   [t16645.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to ACK clusters by using kubectl.md)

## Limits

|Item|Supported version|
|----|-----------------|
|Kubernetes|1.12.6 and later. Only dedicated Kubernetes clusters are supported.|
|Helm|3.0 and later|
|NVIDIA driver|418.87.01 and later|
|Docker|19.03.5|
|Operating system|CentOS 7.x, Alibaba Cloud Linux 2.x, Ubuntu 16.04, and Ubuntu 18.04|
|GPU|Tesla P4, Tesla P100, Tesla T4, and Tesla V100|

## Step 1: Add labels to GPU-accelerated nodes

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Nodes**.

5.  On the Nodes page, click **Manage Labels and Taints** in the upper-right corner.

6.  On the Labels tab of the **Manage Labels and Taints** page, select the nodes that you want to manage and click **Add Label**.

7.  In the Add dialog box, set **Name** and **Value**.

    **Note:**

    -   To enable GPU scheduling, you must set **Name** to **cgpu** and **Value** to **true**.
    -   If you delete the cgpu label, GPU sharing is not disabled. To disable GPU sharing, set **Name** to **cgpu** and **Value** to **false**.
8.  Click **OK**.


## Step 2: Install ack-cgpu on the labeled nodes

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **App Catalog**.

3.  On the App Catalog page, search for ack-cgpu and click ack-cgpu after it appears.

4.  On the App Catalog - ack-cgpu page, select the cluster that you want to manage in the **Deploy** section and click **Create**.

    You do not need to set **Namespace** or **Release Name**. The default values are used.

    ![cgpu](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/5935359951/p101994.png)

    You can run the `helm get manifest cgpu -n kube-system | kubectl get -f -` command to check whether ack-cgpu is installed. If the following output is returned, it indicates that ack-cgpu is installed.

    ```
    helm get manifest cgpu -n kube-system | kubectl get -f -
    ```

    ```
    NAME                                    SECRETS   AGE
    serviceaccount/gpushare-device-plugin   1         39s
    serviceaccount/gpushare-schd-extender   1         39s
    
    NAME                                                           AGE
    clusterrole.rbac.authorization.k8s.io/gpushare-device-plugin   39s
    clusterrole.rbac.authorization.k8s.io/gpushare-schd-extender   39s
    
    NAME                                                                  AGE
    clusterrolebinding.rbac.authorization.k8s.io/gpushare-device-plugin   39s
    clusterrolebinding.rbac.authorization.k8s.io/gpushare-schd-extender   39s
    
    NAME                             TYPE       CLUSTER-IP    EXTERNAL-IP   PORT(S)           AGE
    service/gpushare-schd-extender   NodePort   10.6.13.125   <none>        12345:32766/TCP   39s
    
    NAME                                       DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR    AGE
    daemonset.apps/cgpu-installer              4         4         4       4            4           cgpu=true        39s
    daemonset.apps/device-plugin-evict-ds      4         4         4       4            4           cgpu=true        39s
    daemonset.apps/device-plugin-recover-ds    0         0         0       0            0           cgpu=false   39s
    daemonset.apps/gpushare-device-plugin-ds   4         4         4       4            4           cgpu=true        39s
    
    NAME                                     READY   UP-TO-DATE   AVAILABLE   AGE
    deployment.apps/gpushare-schd-extender   1/1     1            1           38s
    
    NAME                           COMPLETIONS   DURATION   AGE
    job.batch/gpushare-installer   3/1 of 3      3s         38s
    ```


**Related topics**  


[cGPU overview](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/Shared GPU scheduling/cGPU overview.md)

[Enable GPU sharing](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/Shared GPU scheduling/Enable GPU sharing.md)

[Monitor and isolate GPU resources](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/Shared GPU scheduling/Monitor and isolate GPU resources.md)

