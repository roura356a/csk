---
keyword: [Serverless Kubernetes clusters, ASK ARMS, Enable ARMS for a serverless Kubernetes cluster]
---

# Enable ARMS for a serverless Kubernetes cluster

You can connect serverless Kubernetes clusters in different regions to Application Real-Time Monitoring Service \(ARMS\). This allows you to manage the clusters in a unified manner. This topic describes how to enable ARMS for a standard serverless Kubernetes cluster.

[Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md)

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Marketplace** \> **App Catalog**.

3.  On the **App Catalog** page, click the **Alibaba Cloud Apps** tab, and find and click **ack-arms-pilot**.

    In the upper-right corner of the **App Catalog** page, you can enter **ack-arms-pilot** into the Name search bar and click the search icon. You can also enter a keyword to perform a fuzzy match.

4.  On the **App Catalog - ack-arms-pilot** page, select an external cluster in the **Deploy** section to deploy the application.

5.  On the **App Catalog - ack-arms-pilot** page, click the **Parameters** tab, set the parameters, and then click **Create** in the **Deploy** section.

    ![pilot parm](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5465359951/p76784.png)

    |Parameter|Description|
    |---------|-----------|
    |**accessKey**|The AccessKey ID of your Alibaba Cloud account. Your account must be authorized to access ARMS.|
    |**accessKeySecret**|The AccessKey secret of your Alibaba Cloud account.|

    **Note:**

    -   If a leased line is deployed between the cluster and your virtual private cloud \(VPC\), the leased line is automatically used.
    -   If the external cluster is registered through a public network, you must delete vpc in the registry address of the image on the Parameters tab.

Check whether the deployment is successful. For more information, see [Install the ARMS agent for Java applications in Container Service for Kubernetes](/intl.en-US/Application monitoring/Start monitoring Java applications/Install the ARMS agent for Java applications in Container Service for Kubernetes.md).

For information about how to use ARMS, see [ARMS overview](/intl.en-US/Application monitoring/Function specification/Application overview.md).

[Introduction to registered external clusters](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Introduction to registered external clusters.md)

  


