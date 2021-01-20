---
keyword: [GPU topology-aware scheduling, Kubernetes scheduling]
---

# Install the ack-ai-installer component

This topic describes the components and configurations that are required to activate GPU topology-aware scheduling.

-   Create a professional managed Kubernetes cluster. Set Instance Type of the cluster to **Heterogeneous Computing**. For more information, see [Create a professional managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Introduction/Create a professional managed Kubernetes cluster.md).

    **Note:** Only professional managed Kubernetes cluster are supported. If you want to activate GPU topology-aware scheduling for dedicated Kubernetes clusters, [submit a ticket](https://icms.alibaba-inc.com/content/csk/d360da?l=1&m=7459&n=2252239) to add your account to the whitelist.

-   Use kubectl to connect to the Container Service for Kubernetes \(ACK\) cluster. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).
-   The following table lists the required components and versions.

    |Component|Required version|
    |---------|----------------|
    |Kubernetes|V1.18.8 and later|
    |Helm|V3.0 and later|
    |Nvidia|V418.87.01 and later|
    |NVIDIA Collective Communications Library \(NCCL\)|2.7+|
    |Docker|19.03.5|
    |Operating system|CentOS 7.6, CentOS 7.7, Ubuntu 16.04, Ubuntu 18.04, and Alibaba Cloud Linux 2|
    |Graphics card|V100|


1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Marketplace** \> **App Catalog**.

3.  On the App Catalog page, select **Name** from the drop-down list in the upper-right corner of the page, enter ack-ai-installer in the search box, and then click the search icon.

4.  In the **Deploy** pane of the ack-ai-installer page, select the cluster that you want to manage from the **Cluster** drop-down list and click **Create**.![image](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9217528061/p183749.png)


[Overview]()

[Activate GPU topology-aware scheduling in distributed training scenarios]()

