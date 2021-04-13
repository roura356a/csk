# Enable ARMS Prometheus for an external Kubernetes cluster

This topic describes how to enable Application Real-Time Monitoring Service \(ARMS\) Prometheus for an external Kubernetes cluster by deploying an application in the cluster. This provides a unified approach to manage Kubernetes clusters that are deployed across regions.

An external Kubernetes cluster is registered in the Container Service for Kubernetes \(ACK\) console. For more information, see [Create a cluster registration proxy and register an on-premises cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Management of registered clusters/Create a cluster registration proxy and register an on-premises cluster.md).

## Procedure

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **App Catalog**.

3.  On the **App Catalog** page, click the **Alibaba Cloud Apps** tab and find and click **ack-arms-prometheus**.

    In the upper-right corner of the **App Catalog** page, you can enter **ack-arms-prometheus** in to the Name search bar and click the search icon. You can also enter a keyword to perform a fuzzy match.

4.  On the App Catalog - ack-arms-prometheus page, select an external cluster to deploy the application and click **Create** in the **Deploy** section.

    ![Image](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4289693061/p132528.png)

    |Parameter|Description|Remarks|
    |---------|-----------|-------|
    |cluster\_id|The ID of the selected cluster.|These parameters are automatically specified based on the selected cluster.|
    |uid|The user ID of your Alibaba Cloud account.**Note:** The value must be enclosed in quotation marks. |
    |region\_id|The region where ARMS is deployed.|

    **Note:**

    -   If a leased line is deployed between the cluster and virtual private cloud \(VPC\) where the cluster is deployed, the leased line is automatically used.
    -   If the external cluster is registered through a public network, you must delete vpc in the address of the image registry on the Parameters tab. For example, after vpc is deleted, the address of the image registry is registry.cn-hangzhou.aliyuncs.com/arms-docker-repo/arms-prom-operator:v0.1.

After the deployment is complete, you can view monitoring data and customize alert rules. For more information, see [t855956.dita\#concept\_1062555/section\_hg4\_crq\_33b](t855956.dita#concept_1062555/section_hg4_crq_33b) and [Create a Prometheus monitoring alert](/intl.en-US/Dashboard and Alerting/Create an alert.md).

**Related topics**  


[Overview of registered clusters](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Management of registered clusters/Overview of registered clusters.md)

