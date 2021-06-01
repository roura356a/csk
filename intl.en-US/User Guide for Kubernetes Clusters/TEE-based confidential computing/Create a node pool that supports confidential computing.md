---
keyword: [confidential computing, create a node pool]
---

# Create a node pool that supports confidential computing

You can create a node pool that supports confidential computing for a Container Service for Kubernetes \(ACK\) cluster. The node pool functions as a trusted execution environment \(TEE\) that can protect your code and sensitive data from being sniffed or compromised when the code or data is in use. This topic describes how to create a node pool that supports confidential computing.

-   A managed Kubernetes cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md). The created cluster must meet the following requirements:
    -   The network plug-in is Flannel.
    -   The container runtime is containerd.
-   Elastic Compute Service \(ECS\) instances of the **c7t security-enhanced compute optimized** **instance family** are available in the selected region and zone.

    **Note:**

    -   The **security-enhanced compute optimized** instance family is in private preview. This instance family provides a limited stock of ECS instances and is not covered by terms of service level agreement \(SLA\). If you want to use ECS instances of this instance family, apply for ECS instances based on your minimum requirement.
    -   Intel Ice Lake supports the remote attestation service only based on Intel Software Guard Extensions Data Center Attestation Primitives \(SGX DCAP\). Remote attestation services based on Intel Enhanced Privacy Identification \(EPID\) are not supported. You must adapt your applications before you can use the remote attestation service. For more information about the remote attestation service, see [attestation-services](https://software.intel.com/content/www/us/en/develop/topics/software-guard-extensions/attestation-services.html).

TEE-based confidential computing for ACK is powered by Intel SGX 2.0. It provides a cloud-native, all-in-one platform for you to manage and deliver confidential computing applications. Only trusted applications are allowed to run within TEEs. This ensures the security, integrity, and confidentiality of the data that is in use. Confidential computing allows you to isolate sensitive data and code in a TEE. This prevents the data and code from being accessed by the rest of the system. The data stored within TEEs is inaccessible to external applications, the BIOS, the operating system, the kernel, administrators, O&M engineers, cloud service providers, and hardware components except the CPU. This reduces the possibility of data leakage and simplifies data management. You can create a node pool that supports confidential computing in a managed Kubernetes cluster to provide confidential computing for the cluster.

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  In the left-side navigation pane of the details page, choose **Nodes** \> **Node Pools**.

4.  In the upper-right corner of the Node Pools page, click **Create Node Pool**.

    In the upper-right corner of the Node Pools page, you can also click **Create Managed Node Pool** to create a managed node pool, or click **Configure Auto Scaling** to create an auto-scaling node pool.

5.  In the Create Node Pool dialog box, configure the node pool.

    For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md). The following table describes the parameters that are required to create a node pool that supports confidential computing.

    |Parameter|Description|
    |---------|-----------|
    |**Confidential Computing**|Select **Enable**. ![Confidential computing](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6834098161/p132594.png) |
    |**Container Runtime**|To create a node pool that supports confidential computing, you must select the containerd runtime.|
    |**Auto Scaling**|Specify whether to enable auto scaling. If you enable auto scaling, the node pool automatically scales based on resource consumption.|
    |**Instance Type**|Select ECS instances of the **c7t security-enhanced compute optimized** **instance family**.|
    |**Quantity**|Set the initial number of nodes in the node pool. If you do not want to create nodes in the node pool, set this parameter to 0.|
    |**Operating System**|Select only the AliyunLinux 2.xxxx 64-bit \(UEFI\) operating systems.|
    |**Node Label**|You can add labels to the nodes in the node pool.|
    |**ECS Label**|You can add labels to the ECS instances in the node pool.|

6.  Click **Confirm Order**.

    On the Node Pools page, if the **state** of the node pool is **Initializing**, it indicates that the system is creating the node pool.

    On the **Clusters** page, find the cluster in which you create the node pool and click **View Logs** in the **Actions** column. On the page that appears, you can view the log data of the newly created node pool that supports confidential computing.

    ![Cluster log](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6834098161/p132697.png)

    After the node pool is created, the **state** of the node pool changes to **Active**.

    ![Active](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6834098161/p132698.png)


After the node pool that supports confidential computing is created, you can create and deploy applications of Intel SGX 2.0. For more information, see [Use Intel SGX SDK to develop and build applications](/intl.en-US/User Guide for Kubernetes Clusters/TEE-based confidential computing/Use Intel SGX SDK to develop and build applications.md).

