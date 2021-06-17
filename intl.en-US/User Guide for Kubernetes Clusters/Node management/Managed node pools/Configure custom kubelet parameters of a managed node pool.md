---
keyword: [custom kubelet, managed node pools, kube-reserved, system-reserved, kube-api-qps]
---

# Configure custom kubelet parameters of a managed node pool

Container Service for Kubernetes \(ACK\) allows you to configure custom kubelet parameters of a node pool. This way, you can configure custom kubelet parameters for the nodes in the node pool. For example, you can set kube-reserved and system-reserved to reserve computing resources and avoid exhaustion of node resources. When a large number of pods are deployed, you can modify kube-api-qps to improve the performance of kubelet to handle concurrent requests. This topic describes how to configure custom kubelet parameters of a node pool.

-   A professional managed Kubernetes cluster of Kubernetes 1.13 or later is created. Only professional Kubernetes clusters are supported. For more information, see [Create a professional managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Create a professional managed Kubernetes cluster.md).
-   A managed node pool is created. For more information, see [t1999564.md\#section\_s0g\_nbe\_bai](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Managed node pools/Manage managed node pools.md).

kubelet is a core component of Kubernetes that runs as an agent on each node. kubelet runs processes by using binary files. kubelet is also the first service that starts in a Kubernetes cluster.

You can modify kubelet parameters based on your requirements. After the modifications are submitted, the nodes in a node pool will update kubelet in batches. Newly added nodes use the updated kubelet parameters.

## Precautions

-   Modifications to node pools and kubelet parameters are dependent on the custom object **NodePool** defined by Alibaba Cloud and all ConfigMaps in the kube-system namespace. To ensure the stability of node pools, we recommend that you do not modify these resources.
-   Modifications to kubelet parameters cause kubelet processes to restart. During the restart, the node does not remain in the **ready** state and node events also change. If your service or monitoring is strongly dependent on the node state and events, modify kubelet parameters with caution.
-   It requires about 1 minute to update kubelet parameters on a node. During the update, do not perform operations on the node. Otherwise, the update may fail and the kubelet parameters of the node may be rolled back to the previous settings. If an update or rollback fails, you can check the kubelet log or [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to the Alibaba Cloud R&D team.
-   Before you modify kubelet parameters, we recommend that you carefully read the related documentation and understand the parameters to avoid invalid settings. For a complete list of the kubelet parameters and their attributes, see [kubelet](https://github.com/kubernetes/kubernetes/blob/master/staging/src/k8s.io/kubelet/config/v1beta1/types.go).

## Configure custom kubelet parameters of a managed node pool

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Node Pools**.

5.  On the Node Pools page, find the managed node pool that you want to manage and click **Configure kubelet** in the **Actions** column.

6.  In the dialog box, set the parameters and click **OK**.

    The following types of parameters can be set:

    -   qps and burst: Parameters of this type are used to configure traffic throttling of kubelet.
    -   Resource and eviction configurations: Parameters of this type are used to configure and reserve resources, and evict nodes.
    |Type|Parameter|Value type|Example|Feature|
    |----|---------|----------|-------|-------|
    |qps and burst|event-qps|int|5|    -   If the value is greater than 0, it indicates the maximum number of events that can be generated per second.
    -   If you set this parameter to 0, it indicates that the number of events that can be generated per second is unlimited.
Default value: 5. |
    |event-burst|int|10|The maximum number of events that can be reported. This allows event records to temporarily reach an upper limit without exceeding the number specified by event-qps. This parameter is used only if the value of `--event-qps` is greater than 0. Default value: 10. |
    |kube-api-qps|int|5|The maximum number of queries per second \(QPS\) that can be sent to the Kubernetes API server. Default value: 5. |
    |kube-api-burst|int|10|The maximum number of requests that can be forwarded to the Kubernetes API server per second. Default value: 10. |
    |registry-qps|int|5|    -   If the value of `--registry-qps` is greater than 0, it indicates the QPS upper limit of image pulls.
    -   If you set this parameter to 0, it indicates that the QPS of image pulls is unlimited.
Default value: 5. |
    |registry-burst|int|10|The maximum number of bursty image pulls. This allows image pulls to temporarily reach the specified maximum number without exceeding the number specified by registry-qps. This parameter is used only if the value of `--registry-qps` is greater than 0. Default value: 10. |
    |serialize-image-pulls|bool|true|Specifies whether only one image can be pulled at a time. Default value: true.|
    |Resource and eviction configurations|eviction-hard|map\[String\]String|memory.available: 300Minodefs.available: 10%nodefs.inodesFree: 5%imagefs.available: 15%imagefs.inodesFree: 5%pid.available: 100|A set of hard eviction thresholds for pods. Default value of ACK: `--eviction-hard=imagefs.available<15%,memory.available<300Mi,nodefs.available<10%,nodefs.inodesFree<5%`.|
    |eviction-soft|map\[String\]String|Refer to the sample value of eviction-hard|A set of soft eviction thresholds for pods. ACK does not provide default settings. |
    |kube-reserved|map\[String\]String|cpu:100mmemory: 300Miephemeral-storage: 1Gipid: 10000|A set of configurations that specify reserved resources for the Kubernetes system components. Default value of ACK: `--system-reserved=memory=300Mi-system-reserved=pid=10000`.|
    |system-reserved|map\[String\]String|Refer to the sample value of kube-reserved|A set of configurations that specify reserved resources for the system. Default value of ACK: `--kube-reserved=memory=400Mi--kube-reserved=pid=10000`.|

    After kubelet is updated for all nodes in a node pool, the node pool changes to the **Active** state.

    You can run the describe node command to verify that the modifications have been applied to resource reservation parameters. To verify that the modifications have been applied to qps and burst parameters, you must perform stress testing or run commands to check the kubelet log. For example, you can run the `journalctl -u kubelet` command to check the kubelet log.


## Update policy of kubelet parameters

10% of the nodes in a node pool are updated at a time until all the nodes are updated. If the update fails on a node, the update is terminated and this node is rolled back to the previous kubelet configuration.

For example, a node pool has five nodes. In this case, one node is updated at a time. Five updates are required to update all the nodes in the node pool. If the update fails on a node, the update is terminated and this node is rolled back to the previous kubelet configuration. During the rollback, this node remains in the **Updating** state. If you want to continue updating this node, we recommend that you first locate and fix the issue. If a node pool has 100 nodes, 10 nodes are updated at a time. 10 updates are required to update all the nodes based on the preceding description.

