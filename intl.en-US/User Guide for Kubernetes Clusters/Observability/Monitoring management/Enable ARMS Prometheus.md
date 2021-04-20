---
keyword: [enable Prometheus, Prometheus monitoring, enable ARMS Prometheus in ACK]
---

# Enable ARMS Prometheus

You can view preset dashboards and performance metrics of Container Service for Kubernetes \(ACK\) in Application Real-Time Monitoring Service \(ARMS\) Prometheus. This topic describes how to enable ARMS Prometheus in ACK.

ARMS Prometheus is a managed monitoring service that is fully interfaced with the open source Prometheus ecosystem. It monitors a wide variety of components and provides multiple ready-to-use dashboards. ARMS Prometheus saves you the efforts to manage the underlying services, such as data storage, data presentation, and system maintenance.

Compared with the open source Prometheus, Alibaba Cloud Prometheus Service has the following advantages:

-   A lightweight, stable, and precise retry mechanism
    -   Compared with the open source Prometheus, the deployment of Alibaba Cloud Prometheus Service is more lightweight. Instead of building a Prometheus monitoring system, you can install the Prometheus agent \(PromAgent\) to monitor your business.

        ![dg_arms_prometheus_advantage](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6735344851/p76745.png)

    -   The open source Prometheus consumes 16 GB to 128 GB of memory. Alibaba Cloud Prometheus Service consumes only 200 MB to 1 GB of memory and 1 CPU core. Alibaba Cloud Prometheus Service provides higher system stability than the open source Prometheus.
    -   The open source Prometheus retrieves data only once, and data may be discarded when it is written to the storage component. Alibaba Cloud Prometheus Service retries multiple times if it fails to retrieve data. It ensures high concurrency when it writes data to the storage component. No data is discarded.
-   Unlimited data collection and storage
    -   The open source Prometheus can collect data based on up to one million metrics. Alibaba Cloud Prometheus Service can scale its data collection capability based on the number of Kubernetes replicas. Collection tasks can be distributed across replicas.
    -   The maximum storage capacity of the open source Prometheus is limited by the size of the local disk. Alibaba Cloud Prometheus Service uses the central cloud storage service. The storage capacity is not limited.
-   Full compatibility with open source Prometheus
    -   Alibaba Cloud Prometheus Service is compatible with the clients and query languages in the open source Prometheus ecosystem. Alibaba Cloud Prometheus Service provides optimized collection rules and usage values.

        ![dg_arms_prometheus_ecology](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6735344851/p76971.png)

    -   Alibaba Cloud Prometheus Service is compatible with three mainstream collection rules: the open source prometheus.yaml configuration file, ServiceMonitor, and the default collection rule named Annotation. ServiceMonitor is suitable for the monitoring of custom Kubernetes clusters. Compared with the open source Prometheus, Alibaba Cloud Prometheus Service allows you to update collection rules by using the prometheus.yaml configuration file. You do not need to write multiple lines of code in the Deployment file. You only need to add the following three Annotations.

        ```
        prometheus.io/scrape: "true"
        prometheus.io/port: "9090"
        prometheus.io/path: "/metrics"
        ```

    -   Alibaba Cloud Prometheus Service allows you to visualize data by using Grafana. By configuring the Prometheus HTTP API URL, you can implement multi-tenant isolation for the data source in Grafana and multi-tenant isolation for the Grafana dashboard. Alibaba Cloud Prometheus Service is also compatible with the Explore data debugging module of Grafana.
    -   Alibaba Cloud Prometheus Service is compatible with the HTTP API module of the open source Prometheus. It supports three standard API operations: query, query\_range, and labelValues. In addition, you can add /userId/clusterId/regionId/ to the data URL to achieve multi-tenant isolation.
    -   Alibaba Cloud Prometheus Service uses the built-in alerting system of Application Real-Time Monitoring Service \(ARMS\) and is compatible with the alert rules of the open source Prometheus.
-   Cost-effectiveness
    -   Alibaba Cloud Prometheus Service supports the default Kubernetes monitoring. After you install the default Kubernetes monitoring, Alibaba Cloud Prometheus Service automatically creates exporters, collection rules, Grafana dashboards, and ARMS alerts. Compared with the open source Prometheus, Alibaba Cloud Prometheus Service reduces your time cost from about 3 days to about 10 minutes.

        ![Dashboard 1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8885468061/p103080.png)

    -   Alibaba Cloud Prometheus Service supports the monitoring of open source components. You only need to enter the AccessKey ID and AccessKey secret of your Alibaba Cloud account, and the accounts and passwords of the RDS and Redis components. Alibaba Cloud Prometheus Service creates exporters and dashboards for these components. Compared with the open source Prometheus, Alibaba Cloud Prometheus Service reduces your time cost from about 7 days to about 3 minutes.

        ![Dashboard 2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8885468061/p103081.png)

    -   Alibaba Cloud Prometheus Service supports easy installation and removal. You can debug the service by performing health checks. Compared with the open source Prometheus, Alibaba Cloud Prometheus Service reduces your time cost from about 1 day to about 3 minutes.

For more information about ARMS Prometheus, see [ARMS Prometheus overview]().

## Enable ARMS Prometheus

You can enable ARMS Prometheus by using the following methods:

**Configure cluster parameters.**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  In the upper-right corner of the Clusters page, click **Create Kubernetes Cluster**.

4.  Select the cluster template that you want to use and configure parameters for the new cluster. On the **Component Configurations**page, select **Enable Prometheus Monitoring**.

    For more information about how to create a cluster, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

    **Note:** By default, **Enable Prometheus Monitoring** is selected when you create a cluster.

    After the cluster is created, the system automatically configures ARMS Prometheus.


**Enable ARMS Prometheus on the Prometheus Monitoring page in the ACK console.**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the cluster management page, choose **Operations** \> **Prometheus Monitoring**.

5.  In the middle of the **Prometheus Monitoring** page, click **Install**.


**Enable the feature on the App Catalog page in the ACK console.**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Marketplace** \> **App Catalog**.

3.  On the App Catalog page, click the **Alibaba Cloud Apps** tab and click **ack-arms-prometheus**.

4.  On the App Catalog - ack-arms-prometheus page, click the **Parameters** tab.

5.  On the **Parameters** tab, set parameter cluster\_id.

    **Note:**

    The system automatically sets the value to the ID of the cluster that you create. You can perform the following steps to view the cluster ID: In the left-side navigation pane of the ACK console, choose **Clusters** \> **Clusters**. On the page that appears, the cluster ID is displayed below each cluster name.

6.  In the **Deploy** section of the App Catalog - ack-arms-prometheus page, select the cluster for which you want to enable ARMS Prometheus, and click **Create**.

    **Note:** By default, **Namespace** and **Release Name** are set to **arms-prom**.


## Check the result

Log on to the [Prometheus console](https://prometheus.console.aliyun.com/#/home) and click **Prometheus Monitoring** in the left-side navigation pane. Find the cluster for which you have enabled ARMS Prometheus and click a plug-in to view monitoring data in the dashboard.

![deployment](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0255359951/p97179.png)

## FAQ

What can I do if I fail to install ARMS Prometheus again after I delete the namespace of ARMS Prometheus?

If you delete only the namespace of ARMS Prometheus, resource settings may be retained. In this case, you may fail to install ARMS Prometheus again. You can perform the following operations to delete the resource settings:

1.  Delete the ClusterRole.

    ```
    kubectl delete ClusterRole arms-kube-state-metrics
    kubectl delete ClusterRole arms-node-exporter
    kubectl delete ClusterRole arms-prom-ack-arms-prometheus-role
    kubectl delete ClusterRole arms-prometheus-oper3
    ```

2.  Delete the ClusterRoleBinding.

    ```
    kubectl delete ClusterRoleBinding arms-node-exporter
    kubectl delete ClusterRoleBinding arms-prom-ack-arms-prometheus-role-binding
    kubectl delete ClusterRoleBinding arms-prometheus-oper-bind2
    kubectl delete ClusterRoleBinding kube-state-metrics
    ```


**Related topics**  


[View Prometheus Monitoring metrics]()

