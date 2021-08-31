---
keyword: [Windows node pool, create a Windows node pool]
---

# Create a Windows node pool

Container Service for Kubernetes \(ACK\) allows you to use a node pool to manage multiple nodes in a cluster as a group. For example, you can centrally manage the labels and taints of the nodes in a node pool. This topic describes how to create a node pool that consists of Windows nodes in the ACK console.

-   An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   The Kubernetes version of the cluster is later than V1.9.

**Note:**

-   A Windows node pool supports only the Flannel network plug-in and does not support Terway.

-   By default, a cluster can contain at most 100 nodes. To increase the quota, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).
-   When you add an existing Elastic Compute Service \(ECS\) instance to a node pool, make sure that the ECS instance is associated with an elastic IP address \(EIP\) or a NAT gateway is configured for the virtual private cloud \(VPC\) where the ECS instance is deployed. In addition, make sure that the ECS instance has Internet access. Otherwise, you cannot add the ECS instance.

-   Windows node pools support Windows Server 2019, Windows Server Core, Version 1909 and 2004.

    **Note:** You can select Windows Server 2019 and Windows Server Core, Version 2004 in the ACK console. If you want to use Windows Server Core, Version 1909, select the latest image ID based on the release notes for ECS public images to create a node pool. For more information, see [Release notes](/intl.en-US/Images/Public image/Release notes.md).

-   The specifications of ECS instances in a Windows node pool must be higher than 4 vCPU cores and 8 GB of memory.
    -   If the memory usage of Windows containers exceeds the limit, the Out of Memory \(OOM\) Killer is not executed. When the Windows nodes in an ACK cluster whose version is v1.20.4-aliyun.1 or later are started, the Windows nodes reserve the following resources: 1.5 vCPU cores, 2.5 GB RAM, and 3 GB of disk space. These resources are reserved to ensure that the Windows operating system, kubelet, and container runtime can run as normal.

        **Note:** ACK reserves resources in case the Windows workloads overwhelm and then crash the Windows nodes. However, when a memory leak occurs in a Windows container, the Windows node that hosts the container may crash.

    -   Windows containers have a small footprint. For more information, see [Memory requirements of Windows containers](https://docs.microsoft.com/en-us/virtualization/windowscontainers/deploy-containers/system-requirements#memory-requirements).

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Node Pools**.

5.  In the upper-right corner of the Node Pools page, click **Create Node Pool**.

    In the upper-right corner of the Node Pools page, you can also click **Create Managed Node Pool** to create a managed node pool, or click **Configure Auto Scaling** to create an auto-scaling node pool.

6.  In the Create Node Pool dialog box, configure the node pool.

    For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md). The following list describes some of the parameters:

    -   Quantity: Specify the initial number of nodes in the node pool. If you do not want to add nodes to the pool, set this parameter to 0.
    -   Operating System: Select a Windows operating system.
    -   Node Label: Add labels to the nodes in the node pool.
    -   ECS Label: Add labels to the ECS instances.
7.  Click **Confirm Order**.

    On the Node Pools page, if the **state** of the node pool is **initializing**, it indicates that the node pool is being created. After the node pool is created, the **state** of the node pool changes to **active**.

    ![nodepool](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/5365359951/p95881.png)


