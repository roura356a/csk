---
keyword: [enable Prometheus, Prometheus monitoring, enable ARMS Prometheus in ACK]
---

# ARMS Prometheus

You can use Application Real-Time Monitoring Service \(ARMS\) Prometheus to view monitoring dashboards and performance metrics that are preconfigured for Container Service for Kubernetes \(ACK\). This topic describes how to enable ARMS Prometheus in ACK.

ARMS Prometheus is fully compatible with the open source Prometheus ecosystem. It monitors a wide variety of components and provides multiple ready-to-use dashboards. It also provides managed monitoring services. After you enable ARMS Prometheus, you do not need to build a Prometheus monitoring system from scratch. ARMS Prometheus saves you the trouble of managing the underlying services, such as data storage, data presentation, and system operations and maintenance.

-   A lightweight, stable, and precise retry mechanism
-   Unlimited data collection and storage
-   Compatibility with open source Prometheus
-   Cost-effectiveness

For more information about ARMS Prometheus, see [ARMS Prometheus overview]().

## Enable ARMS Prometheus monitoring

You can enable ARMS Prometheus monitoring by using the following three methods:

**Configure cluster parameters.**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click **Create Kubernetes Cluster** in the upper-right corner of the page.

4.  Select the cluster template that you want to use and configure parameters for the new cluster. On the **Component Configurations**page, select **Enable Prometheus Monitoring**.

    For more information about how to create a cluster, see [Create a managed kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed kubernetes cluster.md).

    **Note:** By default, **Enable Prometheus Monitoring** is selected when you create a cluster.

    After the cluster is created, the system automatically configures ARMS Prometheus monitoring.


**Enable the feature on the Prometheus Monitoring page in the ACK console.**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the cluster management page, choose **Operations** \> **Prometheus Monitoring**.

5.  In the middle of the **Prometheus Monitoring** page, click **Install**.


**Enable the feature on the App Catalog page in the ACK console.**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Marketplace** \> **App Catalog**.

3.  On the App Catalog page, click the**Alibaba Cloud Apps** tab and click **ack-arms-prometheus**.

4.  On the App Catalog - ack-arms-prometheus page, click the **Parameters** tab.

5.  On the **Parameters** tab, set parameter cluster\_id.

    **Note:**

    The system automatically sets the value to the ID of the cluster that you create. You can perform the following steps to view the cluster ID: In the left-side navigation pane of the ACK console, choose **Clusters** \> **Clusters**. On the page that appears, the string below each cluster name is the cluster ID.

6.  In the **Deploy** section of the App Catalog - ack-arms-prometheus page, select the cluster for which you want to enable Prometheus monitoring, and click **Create**.

    **Note:** By default, **Namespace** and **Release Name** are set to **arms-prom**.


Log on to the [Prometheus console](https://prometheus.console.aliyun.com/#/home) and click **Prometheus Monitoring**in the left-side navigation pane. Find the cluster for which you enable Prometheus monitoring and click a plug-in to view monitoring data in the dashboard.

![deployment](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0255359951/p97179.png)

**Related topics**  


[View Prometheus Monitoring metrics]()

[Configure data collection rules for Prometheus Monitoring]()

