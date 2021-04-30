---
keyword: [the cGPU component, install the GPU isolation module, kubectl-inspect-cgpu]
---

# Install the cGPU component

Container Service for Kubernetes \(ACK\) provides cGPU to enable graphics processing unit \(GPU\) sharing and scheduling. You can use cGPU to share one GPU in model inference scenarios. In addition, the NVIDIA kernel driver ensures GPU memory isolation among containers. This topic describes how to install the resource isolation module and an inspection tool on a GPU-accelerated node. This enables GPU scheduling and memory isolation.

-   [An ACK cluster with GPU resources is created.](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/Configure a Kubernetes GPU cluster to support GPU scheduling.md)

**Note:** Dedicated Kubernetes clusters and professional Kubernetes clusters support GPU resources. Managed Kubernetes clusters do not support GPU resources.

-   [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md)

## Limits

|Item|Supported version|
|----|-----------------|
|Kubernetes|1.16.06|
|Helm|V3.0 and later|
|NVIDIA driver|V418.87.01 and later|
|Docker|19.03.5|
|Operating system|CentOS 7.6, CentOS 7.7, Ubuntu 16.04, and Ubuntu 18.04|
|GPU|Telsa P4, Telsa P100, Telsa T4, and Telsa V100 \(16 GB\)|

## Step 1: Add labels to GPU-accelerated nodes

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Clusters** \> **Nodes**.

3.  On the Nodes page, select the cluster that you want to manage and click **Manage Labels and Taints** in the upper-right corner of the page.

4.  In the left-side navigation pane, click **Clusters**.

5.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

6.  On the **Nodes** page, click **Manage Labels and Taints** in the upper-right corner of the page.

7.  On the Labels tab of the **Manage Labels and Taints** page, select the nodes that you want to manage and click **Add Label**.

    ![Add labels](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5935359951/p101294.png)

8.  In the Add dialog box, set **Name** and **Value**.

    **Note:**

    -   You must set **Name** to **cgpu** and set **Value** to **true**.
    -   If you delete the cgpu label, GPU sharing is not disabled. To disable GPU sharing, set **Name** to **cgpu** and set **Value** to **false**.
    ![Add labels](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7935359951/p101645.png)

9.  Click **OK**.


## Step 2: Install the cGPU component on the labeled nodes

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **App Catalog**.

3.  On the App Catalog page, find and click **ack-cgpu**.

4.  On the App Catalog - ack-cgpu page, select the cluster that you want to manage in the **Deploy** section on the right side of the page and click **Create**.

    You do not need to set **Namespace** or **Release Name**. The default values are used.

    ![cgpu](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5935359951/p101994.png)

    You can run the `helm get manifest cgpu -n kube-system | kubectl get -f -` command to check whether the cGPU component is installed. If the following output is returned, it indicates that the cGPU component is installed.

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


[Overview](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/Shared GPU scheduling/Overview.md)

[Enable GPU sharing](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/Shared GPU scheduling/Enable GPU sharing.md)

[Monitor and isolate GPU resources](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/Shared GPU scheduling/Monitor and isolate GPU resources.md)

