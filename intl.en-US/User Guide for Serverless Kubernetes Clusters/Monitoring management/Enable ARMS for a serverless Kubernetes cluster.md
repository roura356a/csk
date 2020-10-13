---
keyword: [Serverless Kubernetes clusters, ASK ARMS, Enable ARMS for a serverless Kubernetes cluster]
---

# Enable ARMS for a serverless Kubernetes cluster

You can connect serverless Kubernetes clusters in different regions to Application Real-Time Monitoring Service \(ARMS\). This allows you to manage the clusters in a unified manner. This topic describes how to enable ARMS for a standard serverless Kubernetes cluster.

[Create a serverless Kubernetes cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create a serverless Kubernetes cluster.md)

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Marketplace** \> **App Catalog**.

3.  On the **Alibaba Cloud Apps** tab, click **ack-arms-pilot**.

4.  Click the **Parameters** tab to set parameters.

    ![pilot parm](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5465359951/p76784.png)

    |Parameter|Description|
    |---------|-----------|
    |**accessKey**|Your AccessKey ID.|
    |**accessKeySecret**|Your AccessKey secret.|

    **Note:** If a leased line is deployed between the external cluster and your virtual private cloud \(VPC\), the leased line is automatically used.

5.  In the **Deploy** section on the right, set the parameters and click **Create**.

    -   **Cluster**: Select the cluster where the resource object is to be deployed.
    -   **Namespace**: Select the namespace where the resource object belongs. The default namespace is default. Except for underlying computing resources such as nodes and PVs, most resources are scoped to namespaces.
    -   **Release Name**: The release name of the resource object.

Check whether the deployment is successful. For more information, see [Install the ARMS agent for Java applications in Container Service for Kubernetes](/intl.en-US/Application monitoring/Start monitoring Java applications/Install the ARMS agent for Java applications in Container Service for Kubernetes.md).

For information about how to use ARMS, see [ARMS overview](/intl.en-US/Application monitoring/Function specification/Application overview.md).

[Overview of registered clusters in the ACK console](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Overview of registered clusters in the ACK console.md)

  


