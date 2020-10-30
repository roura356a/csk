---
keyword: [confidential computing, create a node pool]
---

# Create a node pool that supports confidential computing

You can create a node pool that supports confidential computing for a cluster of Container Service for Kubernetes \(ACK\). This creates trusted execution environments \(TEEs\) to store code and sensitive data in your clusters. This way, you can protect your code and data from being sniffed and compromised. This topic describes how to create a node pool that supports confidential computing.

-   A managed Kubernetes cluster is created. For more information, see [Create a cluster of ACK Managed Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Managed Edition.md). The created cluster must meet the following requirements:
    -   The network plug-in is Flannel.
    -   The container runtime is Docker.
-   The cluster is deployed in a region where Elastic Compute Service \(ECS\) Bare Metal instances of the ecs.ebmhfg5.2xlarge type are available for purchase.

ACK provides TEE-based confidential computing, which is a cloud-native and all-in-one solution based on Intel \(R\) Software Guard Extensions \(Intel \(R\) SGX\). It aims to deliver and manage trusted applications and confidential computing applications by ensuring the security, integrity, and confidentiality of data in use. Confidential computing allows you to isolate sensitive data and code in TEEs. This prevents the rest part of the system from accessing the data and code. Encrypted data in a trusted execution environment is unavailable to other applications, the BIOS, the operating system, the kernel, administrators, O&M personnel, cloud vendors, and hardware components except CPUs. This reduces the possibility of sensitive data leaks and simplifies data management. You can create a node pool that supports confidential computing in a managed Kubernetes cluster to provide confidential computing for the cluster.

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**. On the Clusters page, find the cluster in which you want to create a node pool and click **Node Pools** in the **Actions** column.

3.  On the Node Pools page, click **Create Node Pool**.

4.  In the Create Node Pool dialog box, configure the node pool.

    For more information, see [Create a cluster of ACK Managed Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Managed Edition.md). The following table lists the required parameters for a confidential computing node pool.

    |Parameter|Description|
    |---------|-----------|
    |**Confidential Computing**|Select **Enable** to enable confidential computing.|
    |**Container Runtime**|You must select Docker.|
    |**Auto Scaling**|Specify whether to enable Auto Scaling \(ESS\). If you enable ESS, the node pool automatically scales based on the resource consumption.|
    |**Instance Type**|Select **ECS Bare Metal Instance** and select **ecs.ebmhfg5.2xlarge** as the **instance type**.**Note:** You can select multiple instance types. Only the **ecs.ebmhfg5.2xlarge** instance type supports confidential computing. If the stock of ecs.ebmhfg5.2xlarge instances is insufficient, you can select another instance type. However, the node pool does not support confidential computing in this case. |
    |**Quantity**|Specify the initial number of nodes in the node pool. If you do not need to create nodes in the node pool, set this parameter to 0.|
    |**Operating System**|You can select only the Aliyun Linux operating system.|
    |**Node Label**|You can attach labels to nodes in the node pool.|
    |**ECS Label**|You can attach labels to the selected ECS instances.|

5.  Click **OK**.

    On the Node Pools page, if the **status** of the node pool is **Initializing**, it indicates that the node pool creation is in progress.

    On the **Clusters** page, find the cluster and click **View Logs** in the **Actions** column. On the Log Information page, you can view the logs of the newly created node pool that supports confidential computing.

    After the node pool is created, the node pool changes to the **Active****state**.


After the node pool that supports confidential computing is created, you can create and deploy applications by using the Intel\(R\) SGX technology. For more information, see [Use the Intel SGX SDK to develop and build an application](/intl.en-US/User Guide for Kubernetes Clusters/TEE-based confidential computing/Use the Intel SGX SDK to develop and build an application.md).

