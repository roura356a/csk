---
keyword: [confidential computing, create a node pool]
---

# Create a node pool that supports confidential computing

You can create a node pool that supports confidential computing in a Container Service for Kubernetes \(ACK\) cluster to enable confidential computing. This node pool provides a Trusted Execution Environment \(TEE\) that can protect your code and sensitive data from being sniffed or compromised when the code or data is in use. This topic describes how to create a node pool that supports confidential computing.

-   A managed Kubernetes cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md). The created cluster must meet the following requirements:
    -   The network plug-in is Flannel.
    -   The container runtime is Docker.
-   The cluster is deployed in a region where you can purchase ECS Bare Metal instances of the ecs.ebmhfg5.2xlarge type.

TEE-based confidential computing for ACK is powered by Intel Software Guard Extensions \(Intel SGX\). It provides a cloud-native, all-in-one platform for you to develop and manage confidential computing applications. Only trusted applications are allowed to run within TEEs. This ensures the security, integrity, and confidentiality of the data that is in use. Confidential computing allows you to isolate sensitive data and code in a TEE. This prevents the data and code from being accessed by the rest of the system. The data stored within a TEE is inaccessible to external applications, the BIOS, the operating system, the kernel, administrators, O&M engineers, cloud service providers, and hardware components except the CPU. This reduces the possibility of data leakage and simplifies data management. You can create a node pool that supports confidential computing in a managed Kubernetes cluster to provide confidential computing for the cluster.

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  In the left-side navigation pane of the details page, choose **Nodes** \> **Node Pools**.

4.  In the upper-right corner of the Node Pools page, click **Create Node Pool**.

    In the upper-right corner of the Node Pools page, you can also click **Create Managed Node Pool** to create a managed node pool, or click **Configure Auto Scaling** to create an auto-scaling node pool.

5.  In the Create Node Pool dialog box, set the parameters.

    For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md). The following table describes the parameters that are required to create a node pool that supports confidential computing.

    |Parameter|Description|
    |---------|-----------|
    |**Confidential Computing**|Select **Enable** to enable confidential computing. ![Confidential Computing](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6834098161/p132594.png) |
    |**Container Runtime**|Only containerd and Docker support confidential computing. **Note:** The containerd runtime is in public preview. To use containerd, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to apply for it to be available for your account. |
    |**Auto Scaling**|Specify whether to enable auto scaling of the node pool. If you enable auto scaling, the node pool automatically scales based on resource consumption.|
    |**Instance Type**|Select **ECS Bare Metal Instance** and select **ecs.ebmhfg5.2xlarge** as the **instance type**. **Note:** You can select multiple instance types. However, only the **ecs.ebmhfg5.2xlarge** instance type supports confidential computing. If the stock of ecs.ebmhfg5.2xlarge instances is insufficient, you can select another instance type. However, the node pool does not support confidential computing in this case.

![Instance Type](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6834098161/p132689.png) |
    |**Quantity**|Specify the initial number of the nodes in the node pool. If you do not want to create nodes in the node pool, set this parameter to 0.|
    |**Operating System**|You can select only the Aliyun Linux operating system.|
    |**Node Label**|You can add labels to the nodes in the node pool.|
    |**ECS Label**|You can add labels to the ECS instances in the node pool.|

6.  Click **Confirm Order**.

    On the Node Pools page, if the **state** of the node pool is **Initializing**, it indicates that the system is creating the node pool.

    On the **Clusters** page, find the cluster and click **View Logs** in the **Actions** column. On the Log Information page, you can view the logs of the newly created node pool that supports confidential computing.

    ![Cluster log](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6834098161/p132697.png)

    After the node pool is created, the **state** of the node pool changes to **Active**.

    ![Active](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6834098161/p132698.png)


After the node pool that supports confidential computing is created, you can create and deploy Intel SGX applications. For more information, see [Use Intel SGX SDK to develop and build applications](/intl.en-US/User Guide for Kubernetes Clusters/TEE-based confidential computing/Use Intel SGX SDK to develop and build applications.md).

