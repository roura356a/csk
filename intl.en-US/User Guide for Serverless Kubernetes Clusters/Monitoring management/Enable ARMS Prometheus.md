# Enable ARMS Prometheus

You can view metrics on the predefined dashboards of a serverless Kubernetes \(ASK\) cluster through Application Real-Time Monitoring Service \(ARMS\) Prometheus. This topic describes how to enable ARMS Prometheus in ASK.

ARMS Prometheus is a managed monitoring service that is fully compatible with the open source Prometheus ecosystem. It monitors a wide variety of components and provides multiple ready-to-use dashboards. You do not need to be concerned about the management of the underlying services, such as data storage, data presentation, and system operations and maintenance \(O&M\).

Compared with open source Prometheus, ARMS Prometheus has the following benefits:

-   A lightweight, stable, and precise retry mechanism
    -   Compared with the open source Prometheus, the deployment of Alibaba Cloud Prometheus Service is more lightweight. Instead of building a Prometheus monitoring system, you can install the Prometheus agent \(PromAgent\) to monitor your business.

        ![dg_arms_prometheus_advantage](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6735344851/p76745.png)

    -   The open source Prometheus consumes 16 GB to 128 GB of memory. Alibaba Cloud Prometheus Service consumes only 200 MB to 1 GB of memory and 1 CPU core. Alibaba Cloud Prometheus Service provides higher system stability than the open source Prometheus.
    -   The open source Prometheus retrieves data only once, and data may be discarded when it is written to the storage component. Alibaba Cloud Prometheus Service retries multiple times if it fails to retrieve data. It ensures high concurrency when it writes data to the storage component. No data is discarded.
-   Unlimited data collection and storage
    -   The open source Prometheus can collect data based on up to one million metrics. Alibaba Cloud Prometheus Service can scale its data collection capability based on the number of Kubernetes replicas. Collection tasks can be distributed across replicas.
    -   The maximum storage capacity of the open source Prometheus is limited by the size of the local disk. Alibaba Cloud Prometheus Service uses the central cloud storage service. The storage capacity is not limited.
-   Compatibility with open source Prometheus
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
    -   ARMS Prometheus supports the default Kubernetes monitoring. After you install the default Kubernetes monitoring, ARMS Prometheus automatically creates exporters, collection rules, Grafana dashboards, and ARMS alert settings. Compared with open source Prometheus, ARMS Prometheus reduces the time cost from about 3 days to about 10 minutes.

        ![Dashboard 1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8885468061/p103080.png)

    -   ARMS Prometheus supports the monitoring of open source components. You can enter the accounts and passwords of the Relational Database Service \(RDS\) and Redis components. ARMS Prometheus automatically creates exporters and dashboards for these components. Compared with open source Prometheus, ARMS Prometheus reduces the time cost from about 7 days to about 3 minutes.

        ![Dashboard 2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8885468061/p103081.png)

    -   ARMS Prometheus provides easy methods for installation and uninstallation. You can perform health checks to debug the service. Compared with open source Prometheus, ARMS Prometheus reduces your time cost from about 1 day to about 3 minutes.

## Enable ARMS Prometheus

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Marketplace** \> **App Catalog**.

3.  On the App Catalog page, click the **Alibaba Cloud Apps** tab and click **ack-arms-prometheus**.

4.  In the **Deploy** section of the App Catalog - ack-arms-prometheus page, select the cluster for which you want to enable ARMS Prometheus, and click **Create**.

    **Note:** By default, **Namespace** and **Release Name** are set to **arms-prom**.


In the left-side navigation pane, click **Serverless Clusters**. On the Clusters page, click the name of the cluster for which you want to enable ARMS Prometheus or click **Details** in the **Actions** column. In the upper-right corner of the **Overview** tab, click **Prometheus Monitoring** to go to the ARMS Prometheus console. On the **Dashboards** page, find the cluster and click the installed components to view metrics on dashboards.

