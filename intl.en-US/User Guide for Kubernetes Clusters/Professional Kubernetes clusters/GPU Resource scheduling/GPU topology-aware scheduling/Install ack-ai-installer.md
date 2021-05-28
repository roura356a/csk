---
keyword: [topology-aware GPU scheduling, Kubernetes scheduling]
---

# Install ack-ai-installer

This topic describes the components and configurations that are required to activate topology-aware GPU scheduling.

-   A professional Kubernetes cluster is created. When you create the cluster, set Instance Type to **Heterogeneous Computing**. For more information, see [Create a professional managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Create a professional managed Kubernetes cluster.md).

    **Note:** Only professional Kubernetes clusters are supported. If you want to activate topology-aware GPU scheduling for dedicated Kubernetes clusters, [submit a ticket](https://icms.alibaba-inc.com/content/csk/d360da?l=1&m=7459&n=2252239) to be added to the whitelist.

-   You are connected to the cluster by using kubectl. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md).
-   The following table lists the required components and versions.

    |Component|Version|
    |---------|-------|
    |Kubernetes|V1.18.8 and later|
    |Helm|V3.0 and later|
    |Nvidia|V418.87.01 and later|
    |NVIDIA Collective Communications Library \(NCCL\)|2.7+|
    |Docker|19.03.5|
    |Operating system|CentOS 7.6, CentOS 7.7, Ubuntu 16.04 and 18.04, and Alibaba Cloud Linux 2.|
    |GPU|V100|


1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **App Catalog**.

3.  On the App Catalog page, enter ack-ai-installer into the **Name** search box. Click the component after it appears.

4.  In the **Deploy** section of the App Catalog - ack-ai-installer page, select the cluster where you want to deploy the component from the **Cluster** drop-down list and click **Create**.![image](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9217528061/p183749.png)


[Overview of topology-aware GPU scheduling](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/GPU Resource scheduling/GPU topology-aware scheduling/Overview of topology-aware GPU scheduling.md)

[Use GPU topology-aware scheduling to achieve optimal GPU acceleration for TensorFlow distributed jobs](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/GPU Resource scheduling/GPU topology-aware scheduling/Use GPU topology-aware scheduling to achieve optimal GPU acceleration for TensorFlow distributed jobs.md)

[Use GPU topology-aware scheduling to achieve optimal GPU acceleration for PyTorch distributed jobs](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/GPU Resource scheduling/GPU topology-aware scheduling/Use GPU topology-aware scheduling to achieve optimal GPU acceleration for PyTorch distributed jobs.md)

