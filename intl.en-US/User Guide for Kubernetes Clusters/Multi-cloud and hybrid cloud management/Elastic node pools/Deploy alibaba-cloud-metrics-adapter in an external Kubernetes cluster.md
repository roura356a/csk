---
keyword: [external Kubernetes cluster, HPA, alibaba-cloud-metrics-adapter]
---

# Deploy alibaba-cloud-metrics-adapter in an external Kubernetes cluster

This topic describes how to deploy alibaba-cloud-metrics-adapter in an external Kubernetes cluster in the Container Service for Kubernetes \(ACK\) console. The alibaba-cloud-metrics-adapter component collects metrics from your cluster. Horizontal Pod Autoscaler \(HPA\) can scale your application pods based on the collected metrics.

An external Kubernetes cluster is registered in the Container Service for Kubernetes \(ACK\) console. For more information, see [Create a cluster registration proxy and register an on-premises cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Management of registered clusters/Create a cluster registration proxy and register an on-premises cluster.md).

In Kubernetes, metrics are collected to monitor resource usage and performance. In addition, HPA scales the number of pods based on the collected metrics. To meet diverse monitoring requirements of developers, Kubernetes defines the following APIs: resource metrics, custom metrics, and external metrics.

-   Resource metrics are collected by the metrics-server component. The collected metrics are used to monitor the usage of Kubernetes resources, such as pods, nodes, and namespaces.
-   Custom metrics are collected by ARMS Prometheus. HPA scales application pods based on the collected custom metrics.
-   External metrics are collected through the external metrics API provided by the cloud service provider. These metrics are used to monitor the external environment. For example, you can monitor the queries per second \(QPS\) to an Ingress and use HPA to scale application pods when the QPS exceeds the scaling threshold.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **App Catalog**.

3.  On the **App Catalog** page, click the **Alibaba Cloud Apps** tab and find **ack-alibaba-cloud-metrics-adapter**.

    In the upper-right corner of the **App Catalog** page, you can enter **ack-alibaba-cloud-metrics-adapter** into the Name search bar and click the search icon. You can also enter a keyword to perform a fuzzy match.

4.  On the **App Catalog - ack-alibaba-cloud-metrics-adapter** page, select a cluster in the **Deploy** section to deploy the application.

    Use the default values for the **Namespace** and **Release Name** parameters.

5.  On the **App Catalog - ack-alibaba-cloud-metrics-adapter** page, click the **Parameters** tab, set the parameters, and click **Create** in the **Deploy** section.

    ![metric adapter](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7465359951/p103772.png)

    |Parameter|Description|
    |---------|-----------|
    |**AccessKeyId**|The AccessKey ID of your Alibaba Cloud account.|
    |**AccessKeySecret**|The AccessKey secret of your Alibaba Cloud account.|
    |**Region**|The region where your cluster is deployed, for example, cn-qingdao or ap-southeast-1.|

    **Note:** If a leased line is deployed between the cluster and your virtual private cloud \(VPC\), the leased line is automatically used.


**Related topics**  


[Overview of registered clusters](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Management of registered clusters/Overview of registered clusters.md)

