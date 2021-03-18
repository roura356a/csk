---
keyword: [cGPU components, install a GPU isolation module, kubectl-inspect-cgpu]
---

# Install a shared GPU

Container Service for Kubernetes \(ACK\) provides graphics processing unit \(GPU\) sharing and scheduling capabilities. The NVIDIA kernel driver ensures the efficient distribution of the GPU memory that is used by each container. This topic describes how to implement GPU sharing and scheduling. In this case, you must first install a resource isolation module and a GPU scheduling inspection tool on a GPU node.

-   A GPU cluster is created. For more information, see [Create GPU clusters](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU resource scheduling/Configure a Kubernetes GPU cluster to support GPU scheduling.md).

**Note:** Only standard dedicated GPU clusters can be created.

-   [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md)

## Limits

|Item|Supported version|
|----|-----------------|
|Kubernetes|1.16.06|
|Helm|3.0 and later|
|NVIDIA driver|418.87.01 and later|
|Docker|19.03.5|
|Operating system|CentOS 7.6, CentOS 7.7, Ubuntu 16.04, and Ubuntu 18.04|
|Graphics card|Tesla P4, Tesla P100, Tesla T4, and Tesla V100 \(16 GB\)|

## Step 1: Label the nodes on which GPU devices are installed

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Clusters** \> **Nodes**.

3.  On the Nodes page, select the cluster that you want to manage and click **Manage Labels and Taints** in the upper-right corner of the page.

4.  In the left-side navigation pane of the ACK console, click **Clusters**.

5.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

6.  On the **Nodes** page, click **Manage Labels and Taints**.

7.  On the **Labels** tab, select one or more nodes that you want to manage and click **Add Label**.

    ![Add labels](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5935359951/p101294.png)

8.  In the Add dialog box, set **Name** and **Value**.

    **Note:**

    -   You must set **Name** to **cgpu** and set **Value** to **true**.
    -   If you delete the cgpu label, the shared GPU cannot be disabled. You can set **Name** to **cgpu** and set **Value** to **false** to disable the shared GPU.
    ![Add labels](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7935359951/p101645.png)

9.  Click **OK**.


## Step 2: Install a shared cGPU on the labeled nodes

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **App Catalog**.

3.  On the App Catalog page, click **ack-cgpu**.

4.  In the **Deploy** section on the App Catalog - ack-cgpu page, select the required cluster, and click **Create**.

    You do not need to set **Namespace** or **Release Name**. The default values of these parameters are used.

    ![cgpu](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5935359951/p101994.png)

    You can run the `helm get manifest cgpu -n kube-system | kubectl get -f -` command to check whether the cGPU is installed. The following command output indicates that the cGPU is installed.

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


[Overview](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/Shared GPU scheduling/Overview.md)

[Enable GPU sharing](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/Shared GPU scheduling/Enable GPU sharing.md)

[Monitor and isolate GPU resources](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/Shared GPU scheduling/Monitor and isolate GPU resources.md)

